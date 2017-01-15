<!-- Go to www.addthis.com/dashboard to customize your tools -->
<!-- 多说 Share start -->
	<div class="ds-share"
                    style="text-align: right"
                    data-thread-key="{{page.id}}"
                    data-title="{{page.title}}"
                    data-url="{{site.url}}{{site.BASE_PATH}}{{page.url}}"
                    data-images="{{site.url}}{{site.BASE_PATH}}{% if page.header-img %}{{ page.header-img }}{% else %}{{ site.header-img }}{% endif %}"
                    data-content="| {{ page.description }} | {{ site.SEOTitle }}." >
                    <div class="ds-share-inline">
                      <ul  class="ds-share-icons-16">
                        <li data-toggle="ds-share-icons-more"><a class="ds-more" href="#">分享到：</a></li>
                        <li><a class="ds-wechat flat" href="javascript:void(0);" data-service="wechat">微信</a></li>
                        <li><a class="ds-weibo flat" href="javascript:void(0);" data-service="weibo">微博</a></li>
                        <li><a class="ds-douban flat" href="javascript:void(0);" data-service="douban">豆瓣</a></li>
                      </ul>
                      <div class="ds-share-icons-more">
                      </div>
                    </div>
                <hr>
                </div>
 <!-- 多说 Share end-->
				
<!-- 多说评论框 start -->
	<div class="ds-thread" data-thread-key="{{page.id}}" data-title="{{page.title}} | {{site.title}}" data-url="{{site.url}}{{page.url}}"></div>
<!-- 多说评论框 end -->
<!-- 多说公共JS代码 start (一个网页只需插入一次) -->
<script type="text/javascript">
var duoshuoQuery = {short_name:"yiyuzhijian"};
	(function() {
		var ds = document.createElement('script');
		ds.type = 'text/javascript';ds.async = true;
		ds.src = (document.location.protocol == 'https:' ? 'https:' : 'http:') + '//static.duoshuo.com/embed.js';
		ds.charset = 'UTF-8';
		(document.getElementsByTagName('head')[0] 
		 || document.getElementsByTagName('body')[0]).appendChild(ds);
	})();
	</script>
<!-- 多说公共JS代码 end -->

