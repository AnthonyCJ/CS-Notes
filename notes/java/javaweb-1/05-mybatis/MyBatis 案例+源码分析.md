# MyBatis 案例+源码分析

* 提示：使用XML配置文件和注解都可以实现增删改查的功能

  * 注解适用于实现简单功能

  * XML适用于实现复杂功能



要完成的功能清单：

1. 查询
   1. 查询所有数据
   2. 查看详情
   3. 条件查询
2. 添加
3. 修改
   1. 修改全部字段
   2. 修改动态字段
4. 删除
   1. 删除一个
   2. 批量删除





## 1. 配置文件实现CRUD

### 1.1 准备环境

* 数据库表 tb_brand
* 实体类 Brand，创建在包 `pojo` 下
* 测试用例
  * 在Test目录下创建路径`com.anthonycj.javaweb2021.mybatisdemo.test`，创建 MyBatisTest.java
* 安装MyBatisX插件
  * 辅助编写mapper接口和 `XxxMapper.xml`





### 1.2 查询

#### 1.2.1 查询-查询所有数据

1. 编写接口方法：在 `com/anthonycj/javaweb2021/mybatisdemo/mapper` 路径下创建 `BrandMapper`接口
   * 参数：无
   * 结果：`List<Brand>`

~~~java
package com.anthonycj.javaweb2021.mybatisdemo.mapper;

import com.anthonycj.javaweb2021.mybatisdemo.pojo.Brand;

import java.util.List;

public interface BrandMapper {
    /**
     * 查询所有
     */
    List<Brand> selectAll();
}
~~~



2. 创建SQL映射文件，编写SQL语句
   * 在`java`包的`resources`目录下创建路径`com/anthonycj/javaweb2021/mybatisdemo/mapper` ，并创建映射文件`BrandMapper.xml`。
   * 注意，对数据库字段和实体类字段名不一致的属性，使用`resultMap`标签作字段映射，映射后可在SQL语句中使用表中的字段，也可直接使用`*`。

~~~xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--
    namespace:命名空间
-->
<mapper namespace="com.anthonycj.javaweb2021.mybatisdemo.mapper.BrandMapper">

    <!--
        数据库表的字段名称和实体类的属性名称不一致，不能自动封装数据，两种解决方案
            * 起别名：让不一样的属性名和实体属性一样
                * 缺点：每次查询都要起一次别名
                    * 可以使用sql片段包含别名语句
                        * 缺点：不灵活
            * resultMap【推荐使用】
                1. 定义<resultMap>标签
                2. 在<select>标签中使用resultMap属性替换resultType属性
    -->
    
    <resultMap id="brandResultMap" type="brand">
        <!--
            id标签：进行主键字段的映射
                column:表的列名
                property:实体类的属性名
            result标签：进行一般字段的映射
                column:表的列名
                property:实体类的属性名
        -->
        <result column="brand_name" property="brandName"/>
        <result column="company_name" property="companyName"/>
    </resultMap>

    <select id="selectAll" resultMap="brandResultMap">
        select *
        from tb_brand;
    </select>

</mapper>
~~~



3. 编写测试方法`testSelectAll()`，测试

~~~java
@Test
public void testSelectAll() throws IOException {
    // 1.获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    // 2.获取SqlSession对象，用其执行sql
    SqlSession sqlSession = sqlSessionFactory.openSession();

    // 3.获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    // 4.执行方法
    List<Brand> brands = brandMapper.selectAll();
    System.out.println(brands);

    // 5.释放资源
    sqlSession.close();
}
~~~





#### 1.2.2 查询-查询详情（MyBatis单个参数）

1. 编写接口方法：在 `com/anthonycj/javaweb2021/mybatisdemo/mapper` 路径下的 `BrandMapper`接口中添加查询方法。
   * 参数：id
   * 结果：`Brand`

~~~java
/**
     * 查看详情：根据Id查询
     */
Brand selectById(int id);
~~~



2. 在SQL映射文件中编写SQL语句【含参数】
   * 新建`select`标签，设置参数`id="selectById"`，设置参数`resultMap="brandResultMap"`
   * 参数占位符：
     1. `#{}` 会将其替换成 ? ，为了防止SQL注入
     2. `${}` 拼SQL，会存在SQL注入问题
     3. 使用时机
        * 在参数传递时，要用 `#{}`
        * 表名或列名不确定的情况下，用 `${}`，但无法避免SQL注入问题
   * 参数类型：`parameterType="int"`  可以省略
   * 特殊字符处理（xml语法检查不支持'<'等符号）：
     1. 转义字符
     2. CDATA区

~~~xml
<!--
    * 参数占位符：
        1. #{} 会将其替换成 ? ，为了防止SQL注入
        2. ${} 拼SQL，会存在SQL注入问题
        3. 使用时机
            * 在参数传递时，要用 #{}
            * 表名或列名不确定的情况下，用 ${}，但无法避免SQL注入问题
    * 参数类型：parameterType:可以省略
    * 特殊字符处理（xml语法检查不支持'<'等符号）：
        1. 转义字符
        2. CDATA区
-->
<select id="selectById" resultMap="brandResultMap">
    select *
    from tb_brand where id
    <![CDATA[
        =
    ]]>
     #{id};
</select>
~~~



3. 在测试类编写测试方法`testSelectById()`，测试

~~~sql
@Test
public void testSelectById() throws IOException {
    // 接收参数
    int id = 1;

    // 1.获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    // 2.获取SqlSession对象，用其执行sql
    SqlSession sqlSession = sqlSessionFactory.openSession();

    // 3.获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    // 4.执行方法
    Brand brand = brandMapper.selectById(id);
    System.out.println(brand);

    // 5.释放资源
    sqlSession.close();
}
~~~





#### 1.2.3 查询-多条件查询（MyBatis多个参数）

1. 编写接口方法：Mapper接口
   * 参数：所有的查询条件
   * 结果：`List<Brand>`
   * 带参数的接口有三种方式
     1. 散装参数：需要使用`@Param("SQL参数占位符名称") 参数类型 参数名称`
     2. 对象作为参数：对象的属性名称要和参数占位符名称一致
     3. map集合作为参数：SQL中的参数名和map结合的键的名称保持一致

~~~java
/**
 * 条件查询
 * * 参数接收
 *      1. 散装参数：如果方法中有多个参数，需要使用@Param("SQL参数占位符名称")
 *      2. 对象参数：对象的属性名称要和参数占位符名称一致
 *      3. map集合参数：SQL中的参数名和map集合的键的名称保持一致
 * @param status
 * @param companyName
 * @param brandName
 * @return
 */
List<Brand> selectByCondition(@Param("status") int status, @Param("companyName") String companyName, @Param("brandName") String brandName);

List<Brand> selectByCondition(Brand brand);

List<Brand> selectByCondition(Map map);
~~~



2. 在SQL映射文件中编写SQL语句【含参数】

~~~xml
<!--条件查询-->
<select id="selectByCondition" resultMap="brandResultMap">
    select *
    from tb_brand
    where status = #{status}
    and company_name like #{companyName}
    and brand_name like #{brandName}
</select>
~~~



3. 在测试类编写测试方法`testSelectByCondition()`，测试

~~~java
@Test
public void testSelectByCondition() throws IOException {
    // 接收参数
    int status = 1;
    String companyName = "华为";
    String brandName = "华为";

    // 处理参数
    companyName = "%" + companyName + "%";
    brandName = "%" + brandName + "%";

    // 封装对象
    /*Brand brand = new Brand();
    brand.setStatus(status);
    brand.setCompanyName(companyName);
    brand.setBrandName(brandName);*/

    // 封装为Map
    Map map = new HashMap();
    map.put("status", status);
    map.put("companyName", companyName);
    map.put("brandName", brandName);

    // 1.获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    // 2.获取SqlSession对象，用其执行sql
    SqlSession sqlSession = sqlSessionFactory.openSession();

    // 3.获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    // 4.执行方法
    //List<Brand> brands = brandMapper.selectByCondition(status, companyName, brandName);
    //List<Brand> brands = brandMapper.selectByCondition(brand);
    List<Brand> brands = brandMapper.selectByCondition(map);
    System.out.println(brands);

    // 5.释放资源
    sqlSession.close();
}
~~~





#### 1.2.4  查询-多条件-动态条件查询

动态条件查询：为了解决用户输入的条件数量不确定的问题。

* `<if>`标签：用于条件判断
  * `test`属性：该属性值为要判断真假的逻辑表达式，当判断为真时激活if标签包含的语句
  * 对于每个`<if>`标签中的语句，都可以加上and关键字，使用`where`标签后会自动去除多余的关键字
* `<where>`标签：用其替换sql语句中的where关键字
  * 解决的问题：sql语句where子句中第一个条件不需要逻辑运算符

~~~xml
<!--
    动态条件查询
        * if：条件判断
            * test：逻辑表达式
        * 问题，'and' 的存在可能产生语法错误
            * 解决办法：
                1. 恒等式
                2. <where> 标签替换 where 关键字【推荐使用此方法】
-->
<select id="selectByCondition" resultMap="brandResultMap">
    select *
    from tb_brand
    <where>
        <if test="status != null">
            and status = #{status}
        </if>
        <if test="companyName != null and companyName != ''">
            and company_name like #{companyName}
        </if>
        <if test="brandName != null and brandName != ''">
            and brand_name like #{brandName}
        </if>
    </where>
</select>
~~~





#### 1.2.5  查询-单条件-动态条件查询

~~~xml
<select id="selectByConditionSingle" resultMap="brandResultMap">
    
    select *
    from tb_brand
    <where>
        <choose>
            <when test="status != null">/*相当于case*/
                status = #{status}
            </when>
            <when test="companyName != null and companyName != ''">/*相当于case*/
                company_name = #{companyName}
            </when>
            <when test="brandName != null and brandName != ''">/*相当于case*/
                brand_name = #{brandName}
            </when>
            <otherwise>/*相当于default*/
                1 = 1
            </otherwise>
        </choose>
    </where>
    
</select>
~~~





### 1.3  添加



1. 编写接口方法：
   * 参数：除了id之外的所有数据
   * 结果：void
   * 判断是否添加成功：无异常添加成功，有异常则添加失败

~~~java
void add(Brand brand);
~~~



2. 在SQL映射文件中编写SQL语句【含参数】

~~~xml
<insert id="add">
    insert into tb_brand (brand_name, company_name, ordered, description, status)
    values (#{brandName},#{companyName},#{ordered},#{description},#{status})
</insert>
~~~



3. 在测试类编写测试方法`testAdd()`，测试

* MyBatis事务：
  * `openSession()`：默认开启事务，进行增删改操作后需要使用`sqlSession.commit();`手动提交事务
  * `openSession(true)`：可以设置为自动提交事务（关闭事务）

~~~java
@Test
public void testAdd() throws IOException {
    // 接收参数
    String brandName = "iphone";
    String companyName = "苹果";
    int ordered = 100;
    String description = "很贵很贵";
    int status = 1;

    // 封装对象
    Brand brand = new Brand();
    brand.setStatus(status);
    brand.setBrandName(brandName);
    brand.setCompanyName(companyName);
    brand.setOrdered(ordered);
    brand.setDescription(description);

    // 1.获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    // 2.获取SqlSession对象，用其执行sql
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //SqlSession sqlSession = sqlSessionFactory.openSession(true);

    // 3.获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    // 4.执行方法
    brandMapper.add(brand);

    // 提交事务
    sqlSession.commit();

    // 5.释放资源
    sqlSession.close();
}
~~~





#### 1.3.1  添加-主键返回

需求：在数据添加成功后，需要获取插入数据库数据的主键的值

方法：为`<insert>`标签设置属性。

~~~xml
<insert id="add" useGeneratedKeys="true" keyProperty="id">
~~~





### 1.4  修改

#### 1.4.1  修改-修改全部字段

1. 编写接口方法
   * 参数：所有数据
   * 结果：void

~~~java
void update(Brand brand);
~~~



2. 编写SQL语句：SQL映射文件

~~~xml
<update id="update">
    update tb_brand
    set
        brand_name = #{brandName},
        company_name = #{companyName},
        ordered = #{ordered},
        description = #{description},
        status = #{status}
    where
        id = #{id};
</update>
~~~



3. Test方法

~~~java
@Test
public void testUpdate() throws IOException {
    // 接收参数
    String brandName = "iphone";
    String companyName = "苹果";
    int ordered = 100;
    String description = "非常规";
    int status = 1;
    int id = 7;

    // 封装对象
    Brand brand = new Brand();
    brand.setId(id);
    brand.setStatus(status);
    brand.setBrandName(brandName);
    brand.setCompanyName(companyName);
    brand.setOrdered(ordered);
    brand.setDescription(description);

    // 1.获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    // 2.获取SqlSession对象，用其执行sql
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //SqlSession sqlSession = sqlSessionFactory.openSession(true);

    // 3.获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    // 4.执行方法
    int count = brandMapper.update(brand);
    System.out.println(count);

    // 提交事务
    sqlSession.commit();

    // 5.释放资源
    sqlSession.close();
}
~~~





#### 1.4.2 修改-修改动态字段

如果用户输入的数据只有行的部分字段，即只有部分参数有值，则需要动态更新列的值，保证用户未提供参数的列不被更新为 null

解决办法：Mapper.xml中使用`<set>`标签结合`<if>`标签实现动态字段。

1. 编写接口方法
   * 参数：部分数据
   * 结果：void

~~~java
void update(Brand brand);
~~~



2. 编写SQL语句：SQL映射文件
   * `<set>`标签结合`<if>`标签使用
     * `<set>`标签保证sql语句语法正确
       * 没有最后一项时的逗号问题
       * 所有字段都没有值时的set关键字多余问题
     * `<if>`标签实现动态where子句结构（没有该属性时，保证该字段不出现在update语句中）

~~~xml
<update id="update">
    update tb_brand
    <set>
        <if test="brandName != null and brandName != ''">
            brand_name = #{brandName},
        </if>
        <if test="companyName != null and companyName != ''">
            company_name = #{companyName},
        </if>
        <if test="ordered != null">
            ordered = #{ordered},
        </if>
        <if test="description != null and description != ''">
            description = #{description},
        </if>
        <if test="status != null">
            status = #{status}
        </if>
    </set>
    where
        id = #{id};
</update>
~~~



3. Test方法

~~~java
@Test
public void testUpdate() throws IOException {
    // 接收参数
    String brandName = "iphone";
    String companyName = "苹果";
    int ordered = 500;
    String description = "非常规";
    int status = 0;
    int id = 7;

    // 封装对象【此处只提供部分数据，测试动态更新字段功能-set标签和if标签】
    Brand brand = new Brand();
    brand.setId(id);
    brand.setStatus(status);
//        brand.setBrandName(brandName);
//        brand.setCompanyName(companyName);
//        brand.setOrdered(ordered);
//        brand.setDescription(description);

    // 1.获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    // 2.获取SqlSession对象，用其执行sql
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //SqlSession sqlSession = sqlSessionFactory.openSession(true);

    // 3.获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    // 4.执行方法
    int count = brandMapper.update(brand);
    System.out.println(count);

    // 提交事务
    sqlSession.commit();

    // 5.释放资源
    sqlSession.close();
}
~~~





### 1.5 删除

#### 1.5.1 删除-删除一个

1. 编写接口方法
   * 参数：id
   * 结果：void

~~~java
void deleteById(Int id);
~~~



2. 编写SQL语句：SQL映射文件

~~~xml
<delete id="deleteById">
    delete from tb_brand where id = #{id}
</delete>
~~~



3. 编写测试方法

~~~java
@Test
public void testDeleteById() throws IOException {
    // 接收参数，删除id=7的行
    int id = 7;

    // 封装对象

    // 1.获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    // 2.获取SqlSession对象，用其执行sql
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //SqlSession sqlSession = sqlSessionFactory.openSession(true);

    // 3.获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    // 4.执行方法
    brandMapper.deleteById(id);

    // 提交事务
    sqlSession.commit();

    // 5.释放资源
    sqlSession.close();
}
~~~





#### 1.5.2 删除-批量删除

用户勾选不定项的行，系统进行删除。通过数组存储不定项的id，

1. 编写接口方法
   * 参数：id数组
   * 结果：void

~~~java
void deleteByIds(@Param("ids") int[] ids);
~~~

> **为什么此处数组作为参数要使用**`@Param("ids")`
>
> <font color="ff9f44">对于多个参数的接口方法，MyBatis会将接口方法的参数列表，封装为一个Map集合。</font>其中 key 对应参数名称，value对应参数本身，即封装后，可通过参数名称映射出参数值。
>
> 而数组类型在 Map 中默认的 key 是 array，所以在`Mapper.xml`文件中，如果要获取数组参数，则要使用标签属性`collection="array"`来获取数组。
>
> 如果不想使用默认key`"array"`，则可以<font color="ff9f44">使用`Param`注解</font>来自定义参数在 Map 中对应的 key 的名称。比如此处令key的值与参数名一致：`@Param("ids")`



2. 编写SQL语句：SQL映射文件

基本结构如下：

~~~xml
<delete id="deleteByIds">
    delete from tb_brand
    where id in (?,?,?)
</delete>
~~~

存在问题：占位符`?`的数量不确定，sql语句应该是动态变化的，`?`的数量应随着数组内元素的数量变化而变化。要遍历ids数组。

* MyBatis提供了`<foreach>`标签来完成数组或集合的遍历。其中
  * `collection`属性指定要便利的集合或数组
  * `item`属性对应遍历出来的每个元素
  * `separator`属性指定分隔符
  * `open`属性指定开始时拼接内容
  * `close`属性指定结束时拼接内容
  * `#{id}`作为占位符，遍历出几个元素就有几个`?`

改进后实现如下

~~~xml
<delete id="deleteByIds">
    delete from tb_brand
    where id in
    <foreach collection="ids" item="id" separator="," open="(" close=")">
        #{id}
    </foreach>

</delete>
~~~



3. 编写测试方法

~~~java
@Test
public void testDeleteByIds() throws IOException {
    // 接收参数，删除id=7的行
    int[] ids = {3, 6, 7};

    // 封装对象

    // 1.获取SqlSessionFactory
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    // 2.获取SqlSession对象，用其执行sql
    SqlSession sqlSession = sqlSessionFactory.openSession();
    //SqlSession sqlSession = sqlSessionFactory.openSession(true);

    // 3.获取Mapper接口的代理对象
    BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

    // 4.执行方法
    brandMapper.deleteByIds(ids);

    // 提交事务
    sqlSession.commit();

    // 5.释放资源
    sqlSession.close();
}
~~~





### 1.6 MyBatis参数传递

Mybatis 接口方法中可以接收各种各样的参数，如下：

* 多个参数
* 单个参数：单个参数又可以是如下类型
  * POJO 类型
  * Map 集合类型
  * Collection 集合类型
  * List 集合类型
  * Array 类型
  * 其他类型



#### 1.6.1 多个参数

在接口类``Mapper.java`中，如果接口方法定义了多个参数，Mybatis 会将这些参数封装成 Map 集合对象，value 是参数值，而 key 在没有使用 `@Param` 注解时有以下命名规则：

* 以 arg 开头  ：第一个参数就叫 arg0，第二个参数就叫 arg1，以此类推。如：

  > map.put("arg0"，参数值1);
  >
  > map.put("arg1"，参数值2);

* 以 param 开头 ： 第一个参数就叫 param1，第二个参数就叫 param2，依次类推。如：

  > map.put("param1"，参数值1);
  >
  > map.put("param2"，参数值2);

分析：在解析`Mapper.xml`文件时，MyBatis是通过 Map 的映射关系来替换占位符所指代的变量的，所以在编写`Mapper.xml`中的SQL语句时，要使用 Map 中的 key 值代替Mapper接口方法中声明的变量名称。

由于在映射配合文件的SQL语句中使用用 `arg` 开头的和 `param` 书写，代码的可读性会变得很差，所以可以使用 `@Param` 注解来自定义参数名在 Map 中的 key 值。



MyBatis提供了 `ParamNameResolver`类来进行参数封装。其中`public Object getNamedParams(Object[] args)`方法用于执行参数封装过程。

在多个参数的情况下。观察源码可以看到，对于参数列表args中的每一个参数，ByBatis都会在一轮for循环向集合 param 添加2个键值对，一个以 `arg `开头，另一个以 `param` 开头，arg尾号从0开始，param尾号从1开始。如果使用了`@Param`注解，将会替换以 arg 开头的默认 key。

~~~java
/**
 *	@Param: args 对象数组，存放参数列表
 */
public Object getNamedParams(Object[] args) {
    int paramCount = this.names.size();	// 获取参数个数
    if (args != null && paramCount != 0) {	// 如果参数数组为 null 或参数个数为0将返回 null
        if (!this.hasParamAnnotation && paramCount == 1) {	// 如果参数只有1个，则进入 if 分支， 否则进入 else分支
            Object value = args[(Integer)this.names.firstKey()];
            return wrapToMapIfCollection(value, this.useActualParamName ? (String)this.names.get(0) : null);
        } else {
            Map<String, Object> param = new ParamMap();	// Map集合 param 存放参数列表
            int i = 0;
			// 遍历 names 集合，向 param 集合添加键值对
            for(Iterator var5 = this.names.entrySet().iterator(); var5.hasNext(); ++i) {
                Entry<Integer, String> entry = (Entry)var5.next();
                param.put(entry.getValue(), args[(Integer)entry.getKey()]);	// 向 param 集合添加以 arg 开头的键值对
                String genericParamName = "param" + (i + 1);	// 生成以 param 开头的 key 名
                if (!this.names.containsValue(genericParamName)) {
                    param.put(genericParamName, args[(Integer)entry.getKey()]);// 向 param集合添加以 param 开头的键值对
                }
            }

            return param;
        }
    } else {
        return null;
    }
}
~~~

举例说明：

* 对于不带注解的二参数方法

~~~java
User select(String username, String password);
~~~

`getNamedParams(Object[] args)`将执行

~~~java
param.put("arg0",参数值1);
param.put("param1",参数值1);
param.put("arg1",参数值2);
param.put("param2",参数值2);
~~~

在`Mapper.xml`文件中可以如下使用变量参数

~~~xml
<select id="select" resultType="user">
    select *
    from tb_user
    where
        username = #{arg0}	/*或#{param1}*/
      and password = #{arg1}	/*或#{param2}*/
</select>
~~~



* 对于第一个参数1带注解的二参数方法

~~~java
User select(@Param("username") String username, String password);
~~~

`getNamedParams(Object[] args)`将执行

~~~java
param.put("username",参数值1);
param.put("param1",参数值1);
param.put("arg1",参数值2);
param.put("param2",参数值2);
~~~

在`Mapper.xml`文件中可以如下使用变量参数

~~~xml
<select id="select" resultType="user">
    select *
    from tb_user
    where
        username = #{username}	/*或#{param1}*/
      and password = #{arg1}	/*或#{param2}*/
</select>
~~~



<font color="ff9f44">总结：对于多个参数，为增强可读性，强烈建议使用`@Param("参数名") 参数类型 参数名`。</font>





#### 1.6.2 单个参数

* POJO 类型

  直接使用。要求 `属性名` 和 `参数占位符名称` 一致

* Map 集合类型

  直接使用。要求 `map集合的键名` 和 `参数占位符名称` 一致

* Collection 集合类型

  Mybatis 会将集合封装到 map 集合中，如下：

  > map.put("arg0"，collection集合);
  >
  > map.put("collection"，collection集合);

  <font color="ff9f44">可以使用 `@Param` 注解替换map集合中默认的 arg 键名。</font>

* List 集合类型

  Mybatis 会将集合封装到 map 集合中，如下：

  > map.put("arg0"，list集合);
  >
  > map.put("collection"，list集合);
  >
  > map.put("list"，list集合);

  <font color="ff9f44">可以使用 `@Param` 注解替换map集合中默认的 arg 键名。</font>

* Array 类型

  Mybatis 会将集合封装到 map 集合中，如下：

  > map.put("arg0"，数组);
  >
  > map.put("array"，数组);

  <font color="ff9f44">可以使用 `@Param` 注解替换map集合中默认的 arg 键名。</font>

* 其他类型

  比如int类型，`参数占位符名称` 命名为什么都可以。尽量做到见名知意（推荐保持一致）



源码分析

~~~java
/**
 *	@Param: args 对象数组，存放参数列表
 */
public Object getNamedParams(Object[] args) {
    int paramCount = this.names.size();	// 获取参数个数
    if (args != null && paramCount != 0) {	// 如果参数数组为 null 或参数个数为0将返回 null
        if (!this.hasParamAnnotation && paramCount == 1) {	// 如果参数只有1个，则进入 if 分支， 否则进入 else分支
            Object value = args[(Integer)this.names.firstKey()];
            // 如果单个参数为一个 Collection 集合，则调用 wrapToMapIfCollection() 方法，打包成 Map
            return wrapToMapIfCollection(value, this.useActualParamName ? (String)this.names.get(0) : null);
        } else {
            Map<String, Object> param = new ParamMap();	// Map集合 param 存放参数列表
            int i = 0;
			// 遍历 names 集合，向 param 集合添加键值对
            for(Iterator var5 = this.names.entrySet().iterator(); var5.hasNext(); ++i) {
                Entry<Integer, String> entry = (Entry)var5.next();
                param.put(entry.getValue(), args[(Integer)entry.getKey()]);	// 向 param 集合添加以 arg 开头的键值对
                String genericParamName = "param" + (i + 1);	// 生成以 param 开头的 key 名
                if (!this.names.containsValue(genericParamName)) {
                    param.put(genericParamName, args[(Integer)entry.getKey()]);// 向 param集合添加以 param 开头的键值对
                }
            }

            return param;
        }
    } else {
        return null;
    }
}

/**
 * 如果为 Collection 集合则打包成 Map
 *	@Param: args 对象数组，存放参数列表
 */
public static Object wrapToMapIfCollection(Object object, String actualParamName) {
    ParamMap map;
    // 如果参数类型是 Collection，则进入 if 分支
    if (object instanceof Collection) {
        map = new ParamMap();	// 创建 Map 集合对象 map
        map.put("collection", object);	// 将("collection",object)添加至 map 集合
        if (object instanceof List) {
            map.put("list", object);	// 如果传入的参数是List，将("list",object)添加至 map 集合
        }

        Optional.ofNullable(actualParamName).ifPresent((name) -> {
            map.put(name, object);	// 将("arg0",object)添加至 map 集合
        });
        return map;
    } else if (object != null && object.getClass().isArray()) {	// 如果 object 非空，且不是Collection，且是数组
        map = new ParamMap();		// 创建 Map 集合对象 map
        map.put("array", object);	// 将("array",object)添加至 map 集合
        Optional.ofNullable(actualParamName).ifPresent((name) -> {
            map.put(name, object);	// 将("arg0",object)添加至 map 集合
        });
        return map;
    } else {
        return object;	
    }
}
~~~







## 2. 注解实现CRUD

对于注解的使用，官方文档中有如下提示：

* 使用注解来映射简单语句会使代码显得更加简洁，但对于稍微复杂一点的语句，Java 注解不仅力不从心，还会让你本就复杂的 SQL 语句更加混乱不堪。 因此，如果你需要做一些很复杂的操作，最好用 XML 来映射语句。

* 选择何种方式来配置映射，以及认为是否应该要统一映射语句定义的形式，完全取决于你和你的团队。 换句话说，永远不要拘泥于一种方式，你可以很轻松的在基于注解和 XML 的语句映射方式间自由移植和切换。



使用注解的简单示例如下：

```java
@Select(value = "select * from tb_user where id = #{id}")
public User select(int id);
```

> **注意**
>
> * 注解是用来替换映射配置文件方式配置的，所以使用了注解，就不需要再映射配置文件中书写对应的 `statement`

Mybatis 针对 CURD 操作都提供了对应的注解，已经做到见名知意。如下：

* 查询 ：`@Select`
* 添加 ：`@Insert`
* 修改 ：`@Update`
* 删除 ：`@Delete`