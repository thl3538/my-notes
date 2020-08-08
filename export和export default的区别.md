### export和export default的区别



###### export命令用于规定模块外的对外接口

//profile.js

export const TOKEN_KEY = "token";

export var firstName = 'Tom';

第二种写法

const TOKEN_KEY = "token";

var firstName = "Tom";

export { TOKEN_KEY,  firstName };

引入

import { firstName as myName } from './profile.js';

###### export default命令，为模块指定默认输出

//export-default.js

export default function () {

​		console.log("foo");

}

module.js

import  fileName from './export-defalut.js'

