---
title: Laravel笔记
top: 100
keywords:
  - Laravel
  - Eloquent
  - Route
description: 记录平时工作中用到的一些解决问题的方法，Eloquent的操作与Route的操作
tags:
  - Laravel
  - Eloquent
  - Route
categories:
  - Laravel
translate_title: laravel-notes
date: 2019-01-11 14:12:00
---

记录一些平时工作中遇到的问题
开发环境 Homestead：
   php 7.2
   laravel 5.5

<!-- more -->

### 1.路由绑定模型，但模型被软删除
比如我们要查询"/users/1", 但是id为1的用户被软删除了，直接访问"/users/1"的时候会提示404，进行以下操作可以实现用户被软删除后还可以进行查询。
```
# routes/api.php
Route::get('users/{user}', 'UsersController@show')
    ->name('api.users.show');

# 在app/Providers/RouteServiceProvider.php中
public function boot() {
    parent::boot();
    
    Route::bind('user', function ($value) {
        // 只允许详情的查询接口显示被删除的用户的信息，不允许进行更新操作
        if (Route::currentRouteName() == 'api.users.show') {
            return \App\Models\User::withTrashed()->find($value) ?? abort(404);
        }

        return \App\Models\User::whereNull('deleted_at')->find($value) ?? abort(404);
    });
}
```

### 2.Eloquent-withCount
需求：筛选出使用客户端播放音乐时长超过60分钟的门店。
```
# 用户表：stores 表关系：一对多
class Store extends Model {
    ...
    
    public function clientPlayedLogs() {
        return $this->hasMany(ClientPlayedLogs::class);
    }
}

# 客户端播放日志表：client_played_logs 表关系：多对一
$storesQuery = User::with(['clientPlayedLogs']);

$storesQuery->withCount([
    'clientPlayedLogs as client_played_logs_count' => function ($query) use ($startDate, $finishDate) {
        $query->whereBetween('created_at', [$startDate, $finishDate]);
    }
]);

# 注意 havingRow(sqlQuery, arrayParamerters)
$storesQuery->havingRaw('client_played_logs_count / 60 > ?', [60]);

# 还有一件事使用了having以后就不能使用paginate分页了，可以看下面这一节如何having后使用paginate
```

### having后如何使用paginate分页
这个方法也是我在github上看到的，[github地址](https://github.com/justbetter/laravel-pagination-with-havings)，下面把他加入到我们的项目中。

```
cd 项目/app
mkdir Traits && Traits
mkdir PaginationWithHavings && cd PaginationWithHavings
touch BuilderWithPaginationHavingSupport.php && touch PaginationWithHavings.php

# BuilderWithPaginationHavingSupport.php
<?php

namespace App\Traits\PaginationWithHavings;

use Illuminate\Support\Facades\DB;
use Illuminate\Database\Query\Builder;

class BuilderWithPaginationHavingSupport extends Builder
{
    protected function runPaginationCountQuery($columns = ['*'])
    {
        if ($this->havings) {
            $query = $this->cloneWithout(['orders', 'limit', 'offset'])
                          ->cloneWithoutBindings(['order']);

            foreach ($query->columns as $key => $value) {
                if (is_string($value)) {
                    unset($query->columns[$key]);
                }
            }

            $countQuery = DB::table(DB::raw('('.$query->toSql().') as x'))->mergeBindings($query);

            if (!$this->groups) {
                $countQuery->setAggregate('count', $this->withoutSelectAliases($columns));
            }

            return $countQuery->get()->all();
        }

        return $this->cloneWithout(['columns', 'orders', 'limit', 'offset'])
                    ->cloneWithoutBindings(['select', 'order'])
                    ->setAggregate('count', $this->withoutSelectAliases($columns))
                    ->get()->all();
    }
}

# PaginationWithHavings
<?php

namespace App\Traits\PaginationWithHavings;

trait PaginationWithHavings
{
    protected function newBaseQueryBuilder()
    {
        $connection = $this->getConnection();

        return new BuilderWithPaginationHavingSupport(
            $connection,
            $connection->getQueryGrammar(),
            $connection->getPostProcessor()
        );
    }
}

# app/Models/Store
...
use App\Traits\PaginationWithHavings\PaginationWithHavings;
...
class Store extends Model {
    use PaginationWithHavings;
}

# 这样就ok了，以后其他Model需要使用的时候就像Store这个类这样引用Trait就好了
```

### 最后
后面有时间再慢慢更新