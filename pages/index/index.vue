<template>
	<view class="content">
		<view style="text-align: center;margin-top: 20px;">
			弹框提示
		</view>
		<bjx-form label-type="inline" :rules="rules1" label-width="100" :form="form1" ref="form1">
			<bjx-form-item label-type="block" label="姓名" prop="name" :label-right="form1.name.length+'/20'"> 
				<input  v-model="form1.name" class="input" name="input" placeholder="姓名" />
			</bjx-form-item>
			<bjx-form-item label="年龄" label-right="right" prop='age' style='display: inline-block;' width="345"> 
				<input style="width: 220upx;" v-model="form1.age" class="input" name="input" placeholder="年龄" type="number"/>
			</bjx-form-item>
			<bjx-form-item label="身高/cm" prop="height" style='display: inline-block;' width="345" label-width="170"> 
				<input style="width: 170upx;"  v-model="form1.height" class="input" name="input" placeholder="身高" type="number"/>
			</bjx-form-item>

		</bjx-form>
		<button type="primary" @tap="submit(1)">提交</button>
		<view style="text-align: center;margin-top: 20px;">
			页面内提示
		</view>
		<bjx-form label-type="inline" :rules="rules2" label-width="100" :form="form2" ref="form2" msg-type="in" align="right">
			<bjx-form-item label="性别" label-right="right" prop='sex' verticalAlign='top'> 
				<radio-group @change="radioChange" v-model="form2.sex" style="vertical-align: middle;">
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
				<radio-group @change="changType" v-model="form2.type" style="vertical-align: middle;">
					<label class="radio" style="margin-left: 20upx;">
						<radio value="1" checked="true" />手机
					</label>
					<label class="radio" style="margin-left: 20upx;">
						<radio value="2" />邮箱
					</label>
				</radio-group>
			</bjx-form-item>
			<bjx-form-item label="手机" label-right="right" prop='phone' > 
				<input  v-model="form2.phone" class="input" name="input" placeholder="手机"/>
			</bjx-form-item>
			<bjx-form-item label="邮箱" label-right="right" prop='email' > 
				<input  v-model="form2.email" class="input" name="input" placeholder="邮箱"/>
			</bjx-form-item>
			<bjx-form-item label="出生日期" label-right="right" prop='date' > 
				<input  v-model="form2.date" class="input" name="input" placeholder="出生日期"/>
			</bjx-form-item>
		</bjx-form>
		<button type="primary" @tap="submit(2)">提交</button>
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
				form1: {
					name: '',
					age: '',
					height: ''
				},
				rules1: {
					name: {required: true,rule: 'type:string|length:~,20'},
					age: {required: true,rule: 'type:number|between:0,120'},
					height: {required: true,rule: 'type:number|between:10,300'},
				},
				form2: {
					sex: '0',
					type: '1',
					phone: '',
					email: '',
					date: '',
				},
				rules2: {
					sex: {required: true, msg: '请选择性别'},
					phone: {required: true, rule: 'phone', validator: validatePhone},
					email: {required: false},
					date: {required: true, rule: 'date'}
				}
			}
		},
		onLoad() {
		},
		methods: {
			submit(n) {
				// 动态改变 是否必填 状态
				// this.rules2.sex.required = false
				// 校验表单数据
				this.$refs['form' + n].validate(val => {
					console.log(val)
				})
			},
			radioChange(e) {
				this.form2.sex = e.detail.value
			},
			changType(e) {
				this.form2.type = e.detail.value
				console.log(this.rules2)
				this.rules2.phone.required = this.form2.type == '1'
				this.rules2.email.required = this.form2.type == '2'
			}
		}
	}
</script>

<style>
	.content{padding: 30upx;}
</style>
