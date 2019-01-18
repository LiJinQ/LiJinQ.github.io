---
layout:     post
title:      Mybatis-Plus操作分页后数据失效问题
subtitle:   使用PageHelper插件
date:       2019-01-18
author:     LJQ
header-img: img/post-bg-swift.jpg
catalog: true
tags:
    - MyBatis-Plus
    - PageHelper
    - 项目笔记
---

# 业务场景
我们知道在使用PageHelper分页插件时，会对执行```PageHelper.startPage(pageNum, pageSize);```方法后的第一条查询语句进行分页操作。在开发中总会遇到这样的业务情景，在进行分页查询后，需要对获得的列表数据包装成另一种类型，此时需要对新类型的列表进行分页，然而由于```PageInfo<T>```因为泛型的原因，导致处理后的列表不能加入到该类中。
如，我在数据库分页后查询到的类为```PageInfo<User>```，此时改类中的```list```属性为**User**，在当前的业务场景下，我需要将**User**类型替换为**UserVo**类型。
当我们对list属性进行操作时，会同时导致PageInfo类中的其他属性发生改变，如total等。
# 解决方法
~~~Java
        //从数据库获得的Users分页列表
        //当我们打印users时，会发现打印出来的数据除了列表数据还有分页属性
        List<User> users = this.list();
        //操作users获得的需要的列表
        List<UserVo> userVos = this.list2(users);
        //创建User的info类，此时page中的属性已生成
        PageInfo<User> userPage = new PageInfo<>(users);
        //创建UserVo的info类
        PageInfo<UserVo> userVoPage= new PageInfo<>(userVos );
        //将userPage中除看list外的其他属性复制到userVoPage中
        PageInfoUtil pageInfoUtil = new PageInfoUtil();
        pageInfo = pageInfoUtil.getPageVo(userPage ,userVoPage);
~~~

##### pageInfoUtil
~~~Java
public class PageInfoUtil {
    public PageInfo getPageVo(PageInfo pageInfoPo,PageInfo pageInfoVo){
        pageInfoVo.setTotal(pageInfoPo.getTotal());
        pageInfoVo.setEndRow(pageInfoPo.getEndRow());
        pageInfoVo.setHasNextPage(pageInfoPo.isHasNextPage());
        pageInfoVo.setHasPreviousPage(pageInfoPo.isHasPreviousPage());
        pageInfoVo.setIsFirstPage(pageInfoPo.isIsFirstPage());
        pageInfoVo.setIsLastPage(pageInfoPo.isIsLastPage());
        pageInfoVo.setNavigateFirstPage(pageInfoPo.getNavigateFirstPage());
        pageInfoVo.setNavigateLastPage(pageInfoPo.getNavigateLastPage());
        pageInfoVo.setNavigatePages(pageInfoPo.getNavigatePages());
        pageInfoVo.setNavigatepageNums(pageInfoPo.getNavigatepageNums());
        pageInfoVo.setNextPage(pageInfoPo.getNextPage());
        pageInfoVo.setPageNum(pageInfoPo.getPageNum());
        pageInfoVo.setPages(pageInfoPo.getPages());
        pageInfoVo.setPageSize(pageInfoPo.getPageSize());
        pageInfoVo.setPrePage(pageInfoPo.getPrePage());
        pageInfoVo.setSize(pageInfoPo.getSize());
        pageInfoVo.setStartRow(pageInfoPo.getStartRow());
        pageInfoVo.setFirstPage(pageInfoPo.getFirstPage());
        pageInfoVo.setLastPage(pageInfoPo.getLastPage());
        return pageInfoVo;
    }
}

~~~

# End
想使用秀一点的方法，结果能力有限，没有成功，只能使用这种笨方法，如果有更好的方法，欢迎各位指导。