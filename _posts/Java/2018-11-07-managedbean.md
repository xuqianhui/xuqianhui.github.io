---
layout : life
title: managed bean
category : Java
duoshuo: true
date : 2018-11-07
---

	作者: xuqianhui
	版本: V 0.0.1
	日期: 2018年11月07日

<!-- more -->

  * 目录
  {:toc}
## 从选项中选择值（菜单，列表，单选按钮）

菜单之类的通常使用两个bean属性：一个是列表选项，另一个是提交时发送的值：

<h:selectOneMenu value="#{bean1.propertyForSelection}"> 
  <f:selectItems value="#{bean2.propertyForOptions}"/>
</h:selectOneMenu>

### 1.1 输入类型的选择：

h:selectOneMenuh:selectOneListbox（注意box的b为小写）h:selectOneRadio：如果使用<h:selectOneRadio … layout=”pageDirection”>，则单选按钮垂直排列;如果使用layout=”lineDirection，或者省略，则水平排列
Multi-selections：类似的多项选择为：
h:selectManyMenu, h:selectManyListbox, and h:selectManyCheckbox
这些选择使用array 或者 List不是单一的值
h:selectManyMenu在浏览器中的呈现方式十分不同，而且很难在Firefox中使用，大多数人只使用另外两个

### 1.2 更多的细节

<h:selectOneMenu value="#{bean1.selection}"> 
  <f:selectItems value="#{bean2.options}"/>
</h:selectOneMenu>

beans：options和selection不一定要来自于同一个beanOptions：可以是array、List类型（显示和返回的值相同）的值，也可以是Map（keys是显示值）
Map是显示的值。一个选择，返回对应的映射值。通常使用Linkedhashmap来保留排序。也可以让映射值成为复杂的对象，但需要一个转换器,或者一组bean（使用itemLabel 和itemVar）
Array或者List对象：使用itemLabel 选择要显示的对象的一部分，使用 itemValue 选择要返回的对象的一部分

Options中的对象

var：选项中的每个 bean 将依次绑定到此名称

itemLabel：要显示在选项列表中的 bean 的一部分

itemValue：用户选择时返回的 bean 的一部分

<h:selectOneListbox value="#{bean1.selection}"> 
  <f:selectItems value="#{bean2.options}"
                 var="bn" 
                 itemLabel="#{bn.property1}" 
                 itemValue="#{bn.property2}"/> 
</h:selectOneListbox>

f: namespace：必须在页面上方声明<html … xmlns:f=”http://xmlns.jcp.org/jsf/core” …>

## Application Scope

bean的范围：控制managed beans可以保持多久，以及哪些用户和请求可以访问以前的 bean 实例

使用注解或者在faces-config.xml中配置

### 2.1 Request Scope

为每个 http 请求创建一个新的 bean 实例, 无论它是同一用户还是同一页。这是所有 jsf 中最常用的范围
注解：@request scope，但是request scope是默认的，可以省略
<h:inputText value=”#{bankForm.customerId}”/>的几点说明：
bean 为每个提交实例化两次，一次是当表单显示的时候，调用getCustomerId，另一次是当表单提交的时候
如果同一 bean 名称出现在不同的页面上，会使用不同的实例

### 2.2 Application Scope

第一次访问具有该 bean 名称的任何页时, 将对该 bean 进行实例化，从那时起，即使是不同的用户或不同的页面，使用相同的 bean 实例，但是, 不同的 web 应用程序是独立的。
包含用户数据的 bean永远不要使用Application Scope，在本地计算机上使用单个用户进行测试可能会显示正确的结果, 但对于多个同时使用的用户，有一个用户的数据可能会覆盖其他数据
注解：@ApplicationScoped：

Optionally, use with @ManagedBean(eager=true)

此选项使对象在应用程序加载时被实例化
#{messages.message1}：第一次访问此页 (或任何具有该 bean 名称的页) 时, 将对消息进行实例化。从那时起, 相同的 bean 实例用于所有用户和使用该 bean 名称的所有页。

### 2.3 Session Scope

第一次使用 bean 名称的任何页被特定用户访问时, 将对其进行实例化。从那时起, 如果它是相同的 bean 名称和相同的用户, 则使用相同的 bean 实例, 即使它是不同的页面。不同的用户会得到不同的实例

User determined by JSESSIONID cookie (usually) or jsessionid URL parameter (sometimes).
通常用于用户首选项 (字体、颜色、语言)。也用于随着时间推移积累的数据（购物车, 考试问题）

bean 应该是可序列化的, 以便会话数据可以跨服务器重新启动, 因此在群集服务器上, 会话数据可以在节点之间共享。

## Menus, Listboxes等等使用Application Scope

如果选项可以更改：同一个bean使用两次，不是application scoped

<h:selectOneMenu value="#{bean1.propertyForSelection}">
  <f:selectItems value="#{bean1.propertyForOptions}"/>
</h:selectOneMenu>

如果选项总是相同的：

<h:selectOneMenu value="#{bean1（User data bean (not application scoped)）.propertyForSelection}"> 
  <f:selectItems value="#{bean2(application scoped）.propertyForOptions}"/> 
</h:selectOneMenu>

使用静态数据也是合理的选择

## 菜单、列表和单选按钮的预选

Getter方法影响初始选项：
如果用户选择的 getter 方法返回值与其中一个选项匹配, 则是最初显示的值
如果 getter 返回 null 或无匹配值：对于Menus来说,显示最高值;其他的不做选择，也就是说，对于列表框和单选按钮, 总是有匹配的选项

Maps和集合类的对象：getter 应匹配返回值, 而不是显示值