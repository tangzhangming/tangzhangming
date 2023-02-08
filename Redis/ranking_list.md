## Redis实现热门内容排行榜


### Redis有序集合的数据结构
| row | value | scope |
| ------- | ------- | ------- |
|    1    |   10001 | 50 |
|    3    |   10009 | 66 |
|    2    |   10003 | 10000 |

### 原理
可以利用Redis有序集合，value储存内容ID，scope储存浏览数量，取scope最大的前N条，就是热门浏览数据。

需要使用到zincrby、zrevrange命令。
Redis Zrevrange 命令返回有序集中，指定区间内的成员，其中成员的位置按分数值递减(从大到小)来排列，具有相同分数值的成员按字典序的逆序排列。

zrange、zrevrange的区别是根据scope，一个正序，一个倒序

### 代码实例
```php
/**
 * 文章浏览页面代码
 * @url domain.com/article/{article_id}.html
 */
public function detail($id)
{
    //读取文章数据
    $article = ArticleService::getArticleByID($id);
    
    if( !$article ){
        return abort(404, "浏览的内容不存在或者已删除");
    }
    
    /**
     * 1、更新新闻浏览数
     * 2、将当前文章浏览数 +1，存储到对应有序集合的 score 字段
     * 如果统计每日热门，只需要在把key加上日期即可，比如 hot_articles_20230220
     */
    $article->increment('views');
    if ($article->save()) {
        Redis::zincrby('hot_articles', 1, $article->id);
    }
    
    return view('article_detail', [
        'article' => $article
    ]);
}
```


```php
/**
 * 读取网站最热门的10篇文章
 * @url domain.com/article/hot.html
 */
public function hot()
{
    //读取文章数据
    $articleIds = Redis::zrevrange('hot_articles', 0, 9);

    if ($articleIds) {
        $idsStr = implode(',', $articleIds);
        // 查询结果排序必须和传入时的 ID 排序一致
        $articles = ArticleModel::whereIn('id', $postIds)
            ->orderByRaw('field(`id`, ' . $idsStr . ')')
            ->get();

    } else {
        $posts = null;
    }
    
    return view('article_hot', [
        'articles' => $articles
    ]);
}
```
