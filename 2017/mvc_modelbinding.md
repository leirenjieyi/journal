# Mvc Modelbinding
    * mvc通过数据名称和数据反射出来的公共属性的名称进行匹配,匹配成功后将数据映射到参数上。

* mvc 中匹配的顺序一次是
    1. Form data
    2. Route data
    3. url中的查询串

    对于controller
`
    public IActionResult Edit(int? id)
`

    如果表单数据中存在数据名称为 _id_ 值为1,并且url为 recoder/edit/2?id=3,那么最终Edit获得的id值为 *1*。
    对于复杂类型，mvc采用反射和递归来检索类型的属性进行匹配
* 为了使用绑定，参数类型必须提供一个无参构造函数，并且绑定成员应为公共可写属性,当绑定发生时，类的构造函数被调用，公共属性被设置
* 当绑定完一个参数，mvc就会进入下一个参数的绑定，如果绑定失败，mvc不会报错，可以通过检查ModelStat.IsValid属性。
    