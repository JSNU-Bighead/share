# 组合继承
## 原型方法通过inherits方法，属性值构造函数中使用apply方法添加
```js
function inherits (subClass,superClass) {
	function Inner(){}
	Inner.prototype = superClass.prototype;
	subClass.prototype = new Inner();
	subClass.prototype.constructor = subClass;
}
//动物
function Animal (name) {
	this.name = name;
}
Animal.prototype.sayName = function(){alert(this.name)}
//猫科
function Cats (name) {
	Animal.apply(this,arguments);
	this.type = '猫科'
}
inherits(Cats, Animal);
//老虎
function tirger(name) {
	Cats.apply(this,arguments)
}
inherits(tirger, Cats);
var x = new tirger('东北虎')
```
