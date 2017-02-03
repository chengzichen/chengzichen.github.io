#github-page

##github page是什么?

地址:[https://pages.github.com/](https://pages.github.com/)

官方的说明是:   
	     Websites for you and your projects.
    Hosted directly from your GitHub repository. Just edit, push, and your changes are live.

简要说明就是在github上建设自己博客(静态网页).


##怎么创建个人博客站

###在github上创建自己的repository作为博客

**准备工作 :**

 

- 注册一个个人的github账号,账号的名字最好要有意义以后这可能会成为你的个人博客访问的地址(土豪另外去买一个域名的另讲)
 
 
- 熟悉使用git(这个没话讲)


- 熟悉ruby(这个可以慢慢学)
		
参照这里的说明[https://pages.github.com/](https://pages.github.com/)


1. 进入上面的网站点击图中的![](http://i.imgur.com/PgYiMx2.png)
2. 创建一个repository作为工作空间 ![](http://i.imgur.com/26Pm7t2.png)
3. 依次使用git执行

//Clone the repository
    
    git clone https://github.com/username/username.github.io
//Enter the project folder and add an index.html file:

	cd username.github.io
	echo "Hello World" > index.html
 
//Push it

	git add --all
 	git commit -m "Initial commit"
	git push -u origin master

这样就简单的在github上搭起一个简单的页面,距离搭建博客已经很近了
每个帐号只能有一个仓库来存放个人主页，而且仓库的名字必须是username/username.github.io，这是特殊的命名约定。你可以通过http://username.github.io来访问你的个人主页。
通过向导很容易创建一个仓库，并测试成功。不过，同样的，没有博客的结构。需要注意的个人主页的网站内容是在master分支下的。



本文参照:[一步步在GitHub上创建博客主页-最新版](http://www.pchou.info/ssgithubPage/2014-07-04-build-github-blog-page-08.html)
