## `slice函数`

> 使用对象:`数组`





> 展开运算符



`...`:第三个参数是通过展开运算符(`...`)将一个数组转换成多个单独的参数传递给`splice()`方法。

具体来说，该代码片段中第三个参数为：

```javascript
...props.list1.filter((item) => { return item.done })
```

这里使用了ES6的语法，在`list1`数组中筛选出`done`属性值为`true`的元素，并将它们作为一个新数组返回。然后，`...`展开运算符将这个新数组拆分成一个个单独的元素，并传递给`splice()`方法。

因此，整个过程就相当于用`filter()`方法从`list1`数组中筛选出满足条件的元素，然后将这些元素添加到`List`数组中，同时清空了`List`原有的所有元素。

需要注意的是，`splice()`方法会修改原始数组。因此，如果不希望原始数据被修改，可以先复制一份再进行操作。





> `作用: 删除,新增,修改原始数组,复制(不传递引用)`

**特点:**

- `可以传递无限个参数`

- `splice()`方法会修改原始数组。因此，如果不希望原始数据被修改，可以先复制一份再进行操作。

- 不传入参数的时候,表示是原数组的拷贝,`array.slice()`

- splice方法可以接受无限个参数。具体来说，splice方法可以接受三个或更多个参数：

  

1. 第一个参数：表示要删除或插入的起始位置。
2. 第二个参数：表示要删除的元素数量（如果不需要删除元素，则传递0）。
3. 第三个参数及之后的参数：表示要插入到数组中的新元素。

当有第三个参数时，可以通过展开运算符(`...`)来将多个元素作为单独的参数传递给splice方法。这样做可以同时在数组中插入多个元素，并且可以避免手动编写多个push或unshift方法的代码。

需要注意的是，虽然splice方法能够接受无限个参数，但是它并不一定适用于所有情况。如果需要执行复杂的数组操作，可能需要使用其他更适合的方法。



