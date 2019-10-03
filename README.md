> 仅在uin-app 的h5 及 微信小程序 做过测试，因为其他的我没用到，就懒得去调试了


## 参数说明

### 1 bjx-form 组件

> 【校验必须】指需要用到表单数据校验时必须的参数

参数 | 是否必须 | 值类型 | 默认值 | 说明
---|---|---|---|---
ref     |  | String |      | 【校验必须】绑定索引，调用bjx-form 中的校验方法
form     |  | Object |      | 【校验必须】表单数据
rules    |  | Object |      | 【校验必须】表单校验规则，键名应和表单数据保持一致
msg-type |    | String |out | 校验不通过时的提示 out: 弹窗提示；msg: 消息框提示； in: 页面内提示
label-type |  | String |block | label与内容是否在同一行显示 block 不同行 ， inline 同一行
label-width |  | Number \| String |auto | label宽度,为数字时使用 upx 做单位
align |  |  String |left | label的对其方式  left、right、center等
report-submit |  |  Boolean | | 【form自带】是否返回 formId 用于发送模板消息 微信小程序、支付宝小程序可用
@submit |  |  EventHandle | | 【form自带】携带 form 中的数据触发 submit 事件，event.detail = {value : {'name': 'value'} , formId: ''}，report-submit 为 true 时才会返回 formId
@reset |  |  EventHandle | | 【form自带】表单重置时会触发 reset 事件

####  rules 校验规则

参数 | 是否必须 | 值类型 | 默认值 | 说明
---|---|---|---|---
required    |  | Boolean |   false   | 是否必填
msg    |  | String |      | required校验不通过时的提醒文字
rule |    | Array \| String | | 默认校验规则 下面有详细使用方法
message |    | String | | 默认校验或自定义校验不通过时的提醒文字
validator |    | Function | | 自定义校验函数 h5 可用

> rule 默认可以分为多种写法 
注意： 参数是不去空格的，很多时候有空格会影响到校验结果

- 字符串形式 用符号|分隔不同规则，符号: 左边是规则名称，右边是参数，无参数可直接省略，多个参数用,分隔


``` js
例：
// 长度为10 的数字
rule: 'type:number|length:10'
// 长度6-18 的字符串
rule: 'type:string|length:6,18'
// 是否为有效日期
rule: 'date'
// 值 在 a,b,c,0 四个值中
rule: 'in:a,b,c,0'
```

- 数组形式 数组形式下 可以是完全数组形式，也可以在数组中嵌套字符串写法


``` js
// 以 'type:string|length:6,18' 为例
// 可以这样写
rule: ['type:string','length:6,18']
// 也可以这样写
rule: [['type', 'string'],['length','6,18']]
// 还可以这样写
rule: [['type', 'string'],['length',[6,18]]]
```

####   已有的默认校验规则
规则 | 作用 | 参数 | 示例
---|---|---|---
type  | 数据类型校验 | string\|boject\|boolean 等 | type:string
max  | 值小于等于 |  | max:10
min  | 值大于等于 |  | max:0
length  | 数据长度校验 |  | 1. length:2  长度等于某值；2. length:0,2 长度在某区间；3. length:2,~ 长度不小于； 4. length:~,5 长度不大于
between  | 值的范围 |  | 1. 在某区间 between:1,9；2.等于某值 between:10
in  | 校验值在给定的值中 |  | 值在1,3,9三个数当中 in:1,3,9
eq  | 校验值是否与某字段值相等 | eq:字段名 | 如两次密码是否相等 eq:password
date  | 是否是有效日期 | 无需参数 | date
reg  | 正则校验 | 正则表达式 | reg:^1[3\|4\|5\|6\|7\|8][0-9]\d{8,8}$

####   自定义函数

- 写法 支持：h5

``` js
// 函数有两个参数返回 第一个是需要校验的值 第二个是对此参数字段定义的校验规则
// return Boolean | String
// 当返回字符串时，优先做为提示文字 提示文字的优先级 自定义函数msg > 规则中定义的message > 默认提示
const validatePhone = (value, rule) => {
	if(value && !(/^1[3|4|5|6|7|8][0-9]\d{8,8}$/.test(value))){
		return '请填写正确的手机号码' 
	}
	return true
}
// 直接在定义规则时使用
 rules: {
    phone: {required: true,validator: validatePhone},
}

```

- 这种自定义函数写法 仅在h5端通过测试，微信小程序暂不支持，解决办法: 项目文件中有 validate.js 文件 可作为默认规则的扩展，按照格式添加规则函数即可

``` js
// 规则扩展
// 定义的函数 传入三个参数 第一个 val 未校验值 第二个 param 是校验参数  第三个 form 是表单数据
// @return Boolean | String  函数返回ture 则为校验通过 返回false 或者 String 都视为校验不通过
// 返回的字符串优先做为提示文字  提示文字的优先级 自定义函数msg > 规则中定义的message > 默认提示
 export default {
	// 手机号码校验
	phone: function(val) {
		if(val && !(/^1[3|4|5|6|7|8][0-9]\d{8,8}$/.test(val))){
			return '请填写正确的手机号码'
		}
		return true
	}
	// 你的规则
	myValidate: function(val, param, form){
	    if(val){
	        reutrn true
	    }
	    return '不符合规则' || fasle // 不通过返回 二选一
	}
}
```

### 2 bjx-form-item 组件

> bug 在小程序中 下列属性绑定了变量，并动态改变其值时， 均会出现错误提示，如果label要用到动态绑定，暂时可以使用 label 插槽解决

参数 | 是否必须 | 值类型 | 默认值 | 说明
---|---|---|---|---
label    |  | String |      | 数据字段名称 使用插槽时失效
prop    |  | String |      | 表单字段
width |    | Number \| String |auto | form-itme 组件宽度，为数字时使用 upx 做单位
label-width |  | Number \| String | | label 宽度，,为数字时使用 upx 做单位
label-right |  | Number \| String | | 当labelType 为 block 时 label 右侧显示的文字 使用插槽时失效
align |  |  String |left | label的对其方式  left、right、center等
required |  |  Boolean | | 字段名左侧* 是否显示  默认由 校验规则 中的 required 控制
vertical-align |  | String | center |当labelType 为 inline时 label 与右侧内容 垂直对齐方式 可选值： top \| center \| bottom 

#### 2 label 插槽

``` html
// 在bjx-form-item组件中定义一个带 slot="label" 属性的元素即可
// 此时可以使用动态值
<bjx-form-item label-type="block">
	<view slot="label">
		<text>label插槽</text>
		<button style="float: right;" type="primary" size="mini">按钮</button>
	</view>
	<input style="width: 100%;" class="input" name="input" placeholder="label插槽"/>
</bjx-form-item>
```


## 使用说明

### 1 引入并注册两个组件

``` js
import bjxForm from '@/components/bjx-form/bjx-form.vue'
import bjxFormItem from '@/components/bjx-form/bjx-form-item.vue'
export default {
	components: {
		bjxForm,
		bjxFormItem
	}
}
```

### 2 表单数据和校验规则

``` js
export default {
	data() {
	    return {
	        form: {
	            name: '',
	            age: ''
	        },
	        rules: {
	            name: {required: true,rule: 'type:string|length:~,20'},
	            age: {required: true,rule: 'type:number|between:0,120'},
	        }
	    }
	}
}
```

### 3 组件使用

> 注意 两个组件间的嵌套规则 bjx-form 组件的ref属性需绑定一个值 如 form

``` html
<bjx-form
	labelType="inline"
	:rules="rules"
	labelWidth="100"
	:form="form"
	ref="form">
	<bjx-form-item labelType="block" label="姓名" prop="name" :label-right="form.name.length+'/20'"> 
		<input  v-model="form.name" class="input" name="input" placeholder="姓名" />
	</bjx-form-item>
	<view>
		<bjx-form-item label="年龄" label-right="right" prop='age'> 
			<input  v-model="form.age" class="input" name="input" placeholder="年龄" type="number"/>
		</bjx-form-item>
	</view>
	<button type="primary" @tap="submit">提交</button>
</bjx-form>
```

### 4 校验数据

> 父组件直接调用子组件 bjxFrom 中的 validate方法

``` js
export default {
	methods: {
		submit() {
			// 校验表单数据 val 为false 则表明 校验不通过
			// ref="form"
			// this.$refs['form'].validate
			this.$refs.form.validate(val => {
				console.log(val)
			})
		}
	}
}
``` 
