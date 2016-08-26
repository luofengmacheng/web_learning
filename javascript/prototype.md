## prototype原型链继承

前言：

> 相比于C++中的继承，javascript中的prototype原型链继承不太好理解。因此，决心了解下prototype原型链。

### 1 函数与对象

类和对象是C++中的基本概念，使用class关键字创建一个类，然后，可以创建该类的对象。在创建类时，也可以指定该类的父类，此时，当创建该类的对象时，会先调用父类的构造函数，再调用子类的构造函数，以此来创建该对象，在调用方法时，还可以利用多态等特性。

javascript中函数的概念与C++中的类的概念类似，函数既可以当作普通函数调用，又可以理解为类的构造函数。但是，javascript中所有的都是对象，包括函数。

```javascript
function animal(name) {
	this.name = name;
}

var lion = new animal('lion');
console.log(lion.name);
```

如上所示，创建了一个函数，使用该函数创建了一个对象。但是，与C++不同的是，此处使用了this关键字(javascript中this的意义与C++的比较类似，都是指调用的当前对象)，说明就是设定当前对象的属性。然后，在下面就可以使用这个函数创建一个对象。

虽然这里看起来跟C++的差别很大，但是，换个角度看，还是比较好理解的：

C++中，创建对象的第一步就是调用构造函数，同样的，javascript中创建了一个函数，其实就是创建一个类，这个类的构造函数就是该函数本身。

### 2 通过函数创建对象带来的问题

javascript中"一切皆是对象"，当采用上面的方法为"类"添加函数属性时：

```javascript
function animal(name) {
	this.name = name;
	this.cry = function() {
		console.log(this.name + ' cry');
	}
}

var cat = new animal('cat');
var lion = new animal('lion');
```

如上代码所示，创建了一个animal函数，给该函数添加了两个属性，其中一个属性是函数cry(对象)。当创建两个对象时，由于一切皆是对象，那么，cat和lion中分别有两个属性：name和cry，name属性是很好理解的，它类似于C++中的局部变量，而cry则不同，虽然它是函数，但是它也是局部变量，也就是说，cat和lion中都有cry这两个函数(对象)，即，虽然它是个函数，但是，却不被两个cat和lion两个对象共享，而C++中，函数只是一段代码，它是被多个对象共享的。这就会造成系统资源的浪费。

此时的对象示意图如下：

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/prototype1.png)

animal函数包含两个属性，用该函数创建另外两个对象(cat和lion)时，这两个对象复制了animal的所有属性，包括函数和变量。

为了解决这个问题，在给函数添加函数属性时，可以将函数添加到prototype对象中：

```javascript
function animal(name) {
	this.name = name;
}

animal.prototype.cry = function() {
	console.log(this.name + ' cry');
}

var cat = new animal('cat');
var lion = new animal('lion');
```

此时(cat.cry == lion.cry) == true，也就是说，此时cat调用的cry跟lion调用的cry是同一个对象(函数)。

此时的对象示意图如下：

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/prototype2.png)

当cry属性加在animal的prototype原型对象上后，创建cat和lion时，也是复制了animal的所有属性，此时，并没有复制cry。看起来cat和lion上都没有cry属性，但是，还是可以通过cat.cry()和lion.cry()调用cry属性。这就涉及到属性的查找。

javascript中查找属性时，是通过prototype原型链查找的，具体是通过对象`__proto__`属性查找的。

如上图所示，函数对象中都有一个属性：prototype，该属性指向函数的原型对象，而它的原型对象的构造函数又指向函数本身。同时，在每个对象中又包含一个属性：`__proto__`，该属性指向该对象的原型对象。正如上图所看到的，animal函数的prototype属性所表示的对象也就是通过animal函数所创建的对象中`__proto__`所指向的对象。

当通过对象访问属性时，会依据`__proto__`进行查找。例如，cat.cry()，首先会在cat对象中查找是否有cry属性，没有找到，然后就会去cat的`__proto__`指向的对象中查找是否有cry属性，找到了，于是就执行该函数。

这里，再次将javascript与C++进行对比，以加深理解：

* javascript中创建一个函数，就类似于C++中创建一个类，而且该函数就是该类的构造函数。
* javascript的函数中通过this添加属性，这些属性就类似于C++中的非静态成员，也就是说，这些属性不被该函数生成的对象共享，当然也包括函数属性。
* 通过prototype原型对象添加的属性，就类似于C++中的静态属性，这些属性被该函数生成的对象共享。

因此，在javascript中为了实现类似于C++中的成员访问方式，通常会在函数中添加成员变量，而将成员函数添加到函数的prototype原型对象中。