<template>
	<view class="content">
		<view style="text-align: center;margin-bottom: 20px;">
			<radio-group @change="changeMsgType" v-model="form.sex" style="vertical-align: middle;">
				提示方式:
				<label class="radio" style="margin-left: 10upx;">
					<radio value="out" checked="true" />弹框
				</label>
				<label class="radio" style="margin-left: 10upx;">
					<radio value="msg" />消息框
				</label>
				<label class="radio" style="margin-left: 10upx;">
					<radio value="in" />页面内
				</label>
			</radio-group>
		</view>
		<view style="font-weight: bold;text-align: center;">表单</view>
		<bjx-form :class="{'bjx-form-style': msgType!='in'}" label-type="inline" :rules="rules" label-width="100" :form="form" ref="form" :msg-type="msgType">
			<bjx-form-item label-type="block" prop="name">
				<!-- label 中要绑定动态值 请使用插槽 -->
				<view slot="label" >
					<text>姓名</text>
					<text style="float: right;">{{form.name.length || 0}}/20</text>
				</view>
				<input  v-model="form.name" class="input" name="input" placeholder="姓名" />
			</bjx-form-item>
			<bjx-form-item label="年龄" label-right="right" prop='age' style='display: inline-block;' width="345"> 
				<input style="width: 220upx;" v-model="form.age" class="input" name="input" placeholder="年龄" type="number"/>
			</bjx-form-item>
			<bjx-form-item label="身高/cm" prop="height" style='display: inline-block;' width="345" label-width="170"> 
				<input style="width: 170upx;"  v-model="form.height" class="input" name="input" placeholder="身高" type="number"/>
			</bjx-form-item>
			<bjx-form-item label="性别" label-right="right" prop='sex' verticalAlign='top'> 
				<radio-group @change="radioChange" v-model="form.sex" style="vertical-align: middle;">
					<label class="radio" style="margin-left: 20upx;">
						<radio value="1"  />男
					</label>
					<label class="radio" style="margin-left: 20upx;">
						<radio value="2" />女
					</label>
					<label class="radio" style="margin-left: 20upx;">
						<radio value="0" checked="true"/>保密
					</label>
				</radio-group>
			</bjx-form-item>
			<bjx-form-item label="验证方式" label-right="right" prop='sex'> 
				<radio-group @change="changType" v-model="form.type" style="vertical-align: middle;">
					<label class="radio" style="margin-left: 20upx;">
						<radio value="1" checked="true" />手机
					</label>
					<label class="radio" style="margin-left: 20upx;">
						<radio value="2" />邮箱
					</label>
				</radio-group>
			</bjx-form-item>
			<bjx-form-item label="手机" label-right="right" prop='phone' :required="rules.phone.required"> 
				<input  v-model="form.phone" class="input" name="input" placeholder="手机"/>
			</bjx-form-item>
			<bjx-form-item label="邮箱" label-right="right" prop='email' > 
				<input  v-model="form.email" class="input" name="input" placeholder="邮箱"/>
			</bjx-form-item>
			<bjx-form-item label="出生日期" label-right="right" prop='date' labelWidth="180"> 
				<input  v-model="form.date" class="input" name="input" placeholder="出生日期"/>
			</bjx-form-item>
			<bjx-form-item label-type="block">
				<view slot="label" >
					<text>label插槽</text>
					<button style="float: right;" type="primary" size="mini">按钮</button>
				</view>
				<input style="width: 100%;" class="input" name="input" placeholder="label插槽"/>
			</bjx-form-item>
		</bjx-form>
		<button type="primary" @tap="submit">提交</button>
	</view>
</template>

<script>
	import bjxForm from '@/components/bjx-form/bjx-form.vue'
	import bjxFormItem from '@/components/bjx-form/bjx-form-item.vue'
	export default {
		components: {
			bjxForm,
			bjxFormItem
		},
		data() {
			// 自定义校验函数
			const validatePhone = (value, rule) => {
				console.log(value,rule)
				if(value && !(/^1[3|4|5|6|7|8][0-9]\d{8,8}$/.test(value))){
					return '请填写正确的手机号码'
				}
				return true
			}
			return {
				msgType: 'out',
				form:{
					name: '',
					age: '',
					height: '',
					sex: '0',
					type: '1',
					phone: '',
					email: '',
					date: '',
				},
				rules: {
					name: {required: true,rule: 'type:string|length:~,20'},
					age: {required: true,rule: 'type:number|between:0,120'},
					height: {required: true,rule: 'type:number|between:10,300'},
					sex: {required: true, msg: '请选择性别'},
					phone: {required: true, rule: 'phone', validator: validatePhone},
					email: {required: false},
					date: {required: true, rule: 'date'}
				},
			}
		},
		computed: {
			nameLen() {
				return this.form.name.length
			}
		},
		onLoad() {
		},
		methods: {
			submit(n) {
				// 动态改变 是否必填 状态
				// this.rules2.sex.required = false
				// 校验表单数据
				this.$refs.form.validate(val => {
					console.log(val)
				})
			},
			changeMsgType(e) {
				this.msgType = e.detail.value
			},
			radioChange(e) {
				this.form.sex = e.detail.value
			},
			changType(e) {
				this.form.type = e.detail.value
				this.rules.phone.required = this.form.type == '1'
				this.rules.email.required = this.form.type == '2'
			},
			
		}
	}
</script>

<style>
	.content{padding: 30upx;}
	.bjx-form-style .bjx-form-item{margin-bottom: 30upx;}
	.content input {
		border-bottom: solid 1upx #eee;
	}
</style>
