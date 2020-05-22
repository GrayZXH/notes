# Module的语法
模块功能主要由两个命令构成：export 和 import。
export命令用于规定模块的对外接口，import命令用于输入其他模块提供的功能。

## export命令
1. 一个模块就是一个独立的文件。该文件内部的所有变量，外部无法获取。如果你希望外部能够读取模块内部的某个变量，就必须使用export关键字输出该变量。
```
    // export的写法1
    // profile.js
    export var firstName = 'Michael';
    export var lastName = 'Jackson';
    export var year = '1958;
    export function multiply(x,y) {
        return x * y;
    }
```
```
    // export的写法2（更推荐这种写法）
    // profile.js
    var firstName = 'Michael';
    var lastName = 'Jackson';
    var year = 1958;
    function multiply(x,y) {
        return x * y;
    }
    export {firstName,lastName,year,multiply};
``` 
2. 通常情况下，export输出的变量就是本来的名字，但是可以使用as关键字重命名。
```
    function v1() {...}
    function v2() {...}
    export {
        v1 as renameV1,
        v2 as renameV2
    }
```
3. export、import命令可以出现在模块的任何位置，只要处于模块顶层就可以，如果处于块级作用域内，就会报错。

## import命令
1. 使用export命令定义了模块的外接口以后，其他JS文件就可以通过import命令加载这个模块。
```
    // main.js
    import {firstName,lastName,year} form './profile.js';
    function setName(element) {
        element.textContent = firstName + ' ' + lastName;
    }
```
2. 如果想为输入的变量重新取一个名字，import命令要使用as关键字，将输入的变量重命名。
```
    import {lastName as surname} from './profile.js';
```
> import 命令输入的变量都是只读的，因为它的本质是输入接口。如果对其重新赋值就会报错，但是，如果它是个对象，改写它的属性是允许的。但这种写法很难查错，建议凡是输入的变量，都当作完全只读，不要轻易改变它的属性。

3. import后面的form指定模块文件的位置，可以是相对路径，也可以是绝对路径，.js后缀可以省略。如果只是模块名，不带有路径，那么必须有配置文件，告诉JavaScript引擎该模块的位置。
```
import {myMethod} from 'util';
```
4. 同一模块加载多次，将只执行一次。
## 模块的整体加载
除了指定加载某个输出值，还可以使用整体加载，即用星号（ * ）指定一个对象，所有输出值都加载到这个对象上面。
```
    import * as player from './profile.js';
    console.log('FirstName:' + player.firstName);
    console.log('LastName:' + player.lastName);
```
## export default命令
从前面的例子可以看出，使用import命令的时候，用户需要知道所要加载的变量名或函数名，否则无法加载。为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到export default命令，为模块指定默认输出。
```
    // export-default.js
    export default function () {
        console.log('nice to meet you');
    }
```
```
    // import-default.js
    import customName from './export-default';
    customName(); //'nice to meet you'
```
上面代码的import命令，可以用**任意名**称指向export-default.js输出的方法，这时就不需要知道原模块输出的函数名。需要注意的是， **这时 import 命令后面，不使用大括号**
> 本质上，export default就是输出一个叫做default的变量或方法，然后系统允许你为它取任意名字。
如果想在一条import语句中，同时输入默认方法和其他接口，可以写成这样。
```
    import defaultName,{firstName,lastName} from './filePath.js';
```
> 但同时输出 * 和 default时，export * 命令会忽略模块的default方法
## export与import的符合写法
如果在一个模块中，先输入后输出同一个模块，import语句可以与export语句写在一起。
```
    export { foo,bar } from 'my_module';
    // 可以简单理解为
    import { foo,bar } from 'my_module';
    export { foo,bar };
```
## 跨模块常量
const声明的常量只在当前代码块有效。如果想设置跨模块的常量（即跨多个文件），或者说一个值要被多个模块共享。可以建一个专门的constant目录，将各种常量写在不同的文件里面。
```
    // constants/db.js
    export const db = {
        url:'http://my.server.local:5984',
        username: 'admin',
        password: 'password'
    };

    // constants/user.js
    export const users = ['root','admin','staff','ceo']\
```
然后，将这些文件输出的常量，合并在index.js里面。
```
    // constants/index.js
    export {db} from './db';
    export {users} from './users';
```
使用的时候，直接加载index.js就可以了。
```
    // script.js
    import {db,users} from './constants/index';
```