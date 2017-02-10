---
layout:     post
title:      "Material Design 在使用CollapsingToolbarLayout使用和问题"
subtitle:   "CollapsingToolbarLayout是用来对Toolbar进行再次包装的ViewGroup，主要是用于实现折叠（其实就是看起来像伸缩~）的App Bar效果。"
date:     2/9/2017 4:41:44 PM 
author:     "haochen"

header-img: "img/post-bg-unix-linux.jpg"

catalog: true

tags:
    - Material Design
    - CollapsingToolbarLayou
    - 常见问题
    - 2017

---

# CollapsingToolbarLayout


**CollapsingToolbarLayout是用来对Toolbar进行再次包装的ViewGroup，主要是用于实现折叠（其实就是看起来像伸缩~）的App Bar效果。它需要放在AppBarLayout布局里面，并且作为AppBarLayout的直接子View。CollapsingToolbarLayout主要包括几个功能（参照了官方网站上内容，略加自己的理解进行解释）**：

1.  折叠Title（Collapsing title）：当布局内容全部显示出来时，title是最大的，但是随着View逐步移出屏幕顶部，title变得越来越小。你可以通过调用setTitle函数来设置title。

2. 内容纱布（Content scrim）：根据滚动的位置是否到达一个阀值，来决定是否对View“盖上纱布”。可以通过setContentScrim(Drawable)来设置纱布的图片.

3. 状态栏纱布（Status bar scrim)：根据滚动位置是否到达一个阀值决定是否对状态栏“盖上纱布”，你可以通过setStatusBarScrim(Drawable)来设置纱布图片，但是只能在LOLLIPOP设备上面有作用。

4. 视差滚动子View(Parallax scrolling children):子View可以选择在当前的布局当时是否以“视差”的方式来跟随滚动。（PS:其实就是让这个View的滚动的速度比其他正常滚动的View速度稍微慢一点）。将布局参数app:layout_collapseMode设为parallax

5. 将子View位置固定(Pinned position children)：子View可以选择是否在全局空间上固定位置，这对于Toolbar来说非常有用，因为当布局在移动时，可以将Toolbar固定位置而不受移动的影响。 将app:layout_collapseMode设为pin。



# 在Material Design Library包版本问题


 使用"com.android.support:design:23.4.0"就出现了下面的问题提
![](http://i.imgur.com/lnZ2zfx.gif)


但是使用"com.android.support:design:25.0.0"就没有这个问题了
![](http://i.imgur.com/YQBUpAM.gif)


**这是相关代码:**

	<?xml version="1.0" encoding="utf-8"?>
	<layout xmlns:android="http://schemas.android.com/apk/res/android"
	        xmlns:app="http://schemas.android.com/apk/res-auto"
	        xmlns:tools="http://schemas.android.com/tools">
	
	    <android.support.v4.widget.DrawerLayout
	        android:id="@+id/dl_main_drawer"
	        android:layout_width="match_parent"
	        android:layout_height="match_parent"
	        android:background="@color/alpha_white"
	        android:fitsSystemWindows="true"
	        tools:openDrawer="start">
	
	        <android.support.design.widget.CoordinatorLayout
	            android:id="@+id/main_content"
	            android:layout_width="match_parent"
	            android:layout_height="match_parent"
	            android:fitsSystemWindows="true">
	
	            <android.support.design.widget.AppBarLayout
	                android:id="@+id/appbar"
	                android:layout_width="match_parent"
	                android:layout_height="250dp"
	                android:fitsSystemWindows="true"
	                android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar"
	                >
	                <!--app:layout_behavior="android.support.design.widget.AppBarLayoutSpringBehavior"-->
	
	                <android.support.design.widget.CollapsingToolbarLayout
	                    android:id="@+id/collapsing_toolbar"
	                    android:layout_width="match_parent"
	                    android:layout_height="match_parent"
	                    android:fitsSystemWindows="true"
	                    app:contentScrim="?attr/colorPrimary"
	                    app:expandedTitleMarginBottom="64dp"
	                    app:expandedTitleMarginEnd="16dp"
	                    app:expandedTitleMarginStart="16dp"
	                    app:layout_scrollFlags="scroll|exitUntilCollapsed">
	
	                    <android.support.percent.PercentFrameLayout
	                        android:layout_width="match_parent"
	                        android:layout_height="match_parent"
	                        android:layout_gravity="center_horizontal"
	                        android:fitsSystemWindows="true"
	                        app:layout_collapseMode="parallax"
	                        app:layout_collapseParallaxMultiplier="0.7">
	
	                        <ImageView
	                            android:id="@+id/toolbar_iv_outgoing"
	                            android:layout_width="0dp"
	                            android:layout_height="match_parent"
	                            android:layout_gravity="center_horizontal"
	                            android:adjustViewBounds="true"
	                            android:contentDescription="@null"
	                            android:fitsSystemWindows="true"
	                            android:scaleType="centerCrop"
	                            android:visibility="gone"
	                            app:layout_widthPercent="120%" />
	
	                        <ImageView
	                            android:id="@+id/toolbar_iv_target"
	                            android:layout_width="0dp"
	                            android:layout_height="match_parent"
	                            android:layout_gravity="center_horizontal"
	                            android:adjustViewBounds="true"
	                            android:contentDescription="@null"
	                            android:fitsSystemWindows="true"
	                            android:scaleType="centerCrop"
	                            app:layout_collapseMode="parallax"
	                            app:layout_widthPercent="120%" />
	
	                    </android.support.percent.PercentFrameLayout>
	
	                    <android.support.v7.widget.Toolbar
	                        android:id="@+id/toolbar"
	                        android:layout_width="match_parent"
	                        android:layout_height="?attr/actionBarSize"
	                        android:layout_marginBottom="48dp"
	                        app:layout_collapseMode="pin"
	                        app:popupTheme="@style/AppTheme.PopupOverlay" />
	
	                    <android.support.design.widget.TabLayout
	                        android:id="@+id/tabs"
	                        android:layout_width="match_parent"
	                        android:layout_height="wrap_content"
	                        android:layout_gravity="bottom"
	                        app:tabGravity="fill"
	                        app:tabIndicatorColor="@color/white"
	                        app:tabMode="scrollable" />
	
	                </android.support.design.widget.CollapsingToolbarLayout>
	
	
	            </android.support.design.widget.AppBarLayout>
	
	            <android.support.v4.widget.NestedScrollView
	                android:id="@+id/nestedScrollView"
	                android:layout_width="match_parent"
	                android:layout_height="match_parent"
	                android:fillViewport="true"
	                app:layout_behavior="@string/appbar_scrolling_view_behavior">
	
	                <android.support.v4.view.ViewPager
	                    android:id="@+id/viewpager"
	                    android:layout_width="match_parent"
	                    android:layout_height="match_parent" />
	            </android.support.v4.widget.NestedScrollView>
	
	            <android.support.design.widget.FloatingActionButton
	                android:id="@+id/fab"
	                android:layout_width="wrap_content"
	                android:layout_height="wrap_content"
	                android:layout_gravity="end|bottom"
	                android:layout_margin="@dimen/fab_margin"
	                />
	            <!--android:background="@drawable/button_ripple_blue"-->
	            <!--app:layout_behavior="com.view.behavior.MyFabBehavior"-->
	            <!--app:srcCompat="@drawable/ic_add"-->
	
	        </android.support.design.widget.CoordinatorLayout>
	
	        <android.support.design.widget.NavigationView
	            android:id="@+id/nv_main_navigation"
	            android:layout_width="wrap_content"
	            android:layout_height="match_parent"
	            android:layout_gravity="start"
	            android:background="@color/alpha_white"
	            android:fitsSystemWindows="true"
	            app:headerLayout="@layout/nav_header_main"
	            app:menu="@menu/activity_main_drawer" />
	
	    </android.support.v4.widget.DrawerLayout>
	</layout>



## 其他的使用:

- 如果你希望拖动过程中状态栏是透明的，可以在CollapsingToolbarLayout中加 app:statusBarScrim="@android:color/transparent"，并且在onCreate中调用getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS)将状态栏设置为透明就好啦~



- CollapsingToolbarLayout可以通过app:contentScrim设置折叠时工具栏布局的颜色，通过app:statusBarScrim设置折叠时状态栏的颜色。默认contentScrim是colorPrimary的色值，statusBarScrim是colorPrimaryDark的色值。这个后面会用到。

- CollapsingToolbarLayout的子布局有3种折叠模式（Toolbar中设置的app:layout_collapseMode）

(1) off：这个是默认属性，布局将正常显示，没有折叠的行为。

(2): pin：CollapsingToolbarLayout折叠后，此布局将固定在顶部。
(3): parallax：CollapsingToolbarLayout折叠时，此布局也会有视差折叠效果。
当CollapsingToolbarLayout的子布局设置了parallax模式时，我们还可以通过app:layout_collapseParallaxMultiplier设置视差滚动因子，值为：0~1。



- TabLayout没有设置app:layout_collapseMode，在CollapsingToolbarLayout收缩时就不会消失。

- CollapsingToolbarLayout收缩时的高度是Toolbar的高度，所以我们需要把Toolbar的高度增加，给TabLayout留出位置，这样收缩后TabLayout就不会和Toolbar重叠。

- Toolbar的高度增加，title会相应下移。android:gravity="top"方法使Toolbar的title位于Toolbar的上方，然后通过app:titleMarginTop调整下title距顶部高度，这样Toolbar就和原来显示的一样了。