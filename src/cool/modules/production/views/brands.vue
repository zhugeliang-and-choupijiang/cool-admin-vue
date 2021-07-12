<template>
	<div class="demo">
		<cl-crud :ref="setRefs('crud')" :on-refresh="onRefresh" @load="onLoad">
			<el-row type="flex">
				<cl-refresh-btn />
				<cl-add-btn>添加员工</cl-add-btn>
				<cl-flex1 />
				<!-- <cl-search-key field="name" :field-list="[{ label: '名称', value: 'name' }]" /> -->
			</el-row>

			<el-row>
				<!-- 数据表格 -->
				<cl-table :ref="setRefs('table')" v-bind="table">
					<template #column-img="{ scope }">
						<img :src="scope.row.img" min-width="70" height="70" />
					</template>
				</cl-table>
			</el-row>

			<el-row>
				<cl-flex1 />
				<cl-pagination />
			</el-row>

			<cl-upsert :ref="setRefs('upsert')" :items="upsert.items" :on-submit="onUpsertSubmit" />
		</cl-crud>
	</div>
</template>

<script lang="ts">
import { defineComponent, inject, reactive } from "vue";
import { CrudLoad, RefreshOp, Table, Upsert } from "cl-admin-crud-vue3/types";
import moment from "moment";
import { useRefs } from "/@/core";

export default defineComponent({
	name: "brands",

	components: {},

	setup() {
		const service = inject<any>("service");
		const { refs, setRefs } = useRefs();
		// crud 加载
		function onLoad({ ctx, app }: CrudLoad) {
			ctx.service(service.production.brands)
				.set("dict", {
					api: {
						page: "collections"
					}
				})
				.done();
			app.refresh();
		}

		// 提交钩子
		function onUpsertSubmit(_: boolean, data: any, { next }: any) {}

		// 刷新钩子
		function onRefresh(params: any, { next, render }: RefreshOp) {
			next(params).then((res: any) => {
				render(res, {
					total: res.length
				});
			});
		}

		function timeformat(row: any, column: any) {
			var time = row[column.property];
			if (time === undefined) {
				return "";
			}
			return moment.utc(parseInt(time)).local().format("YYYY-MM-DD HH:mm:ss");
		}

		function refresh(params: any) {
			refs.value.crud.refresh(params);
		}

		// 表格配置
		const table = reactive<Table>({
			props: {
				"default-sort": {
					prop: "createTime",
					order: "descending"
				}
			},
			columns: [
				{
					label: "名称",
					prop: "name",
					minWidth: 70
				},
				{
					label: "作者",
					prop: "author",
					minWidth: 70
				},
				{
					label: "collection名称",
					prop: "collection_name",
					minWidth: 100
				},
				{
					label: "品牌图",
					prop: "img",
					minWidth: 150
				},
				{
					label: "费用",
					prop: "market_fee",
					minWidth: 110
				},
				{
					label: "创建时间",
					prop: "created_at_time",
					width: 150,
					formatter: timeformat
				}
			]
		});

		// 新增、编辑配置
		const upsert = reactive<Upsert>({
			items: [
				{
					prop: "headImg",
					label: "头像",
					span: 24,
					component: {
						name: "cl-upload",
						props: {
							text: "选择头像",
							icon: "el-icon-picture"
						}
					}
				},
				{
					prop: "name",
					label: "姓名",
					span: 12,
					component: {
						name: "el-input",
						props: {
							placeholder: "请填写姓名"
						}
					},
					rules: {
						required: true,
						message: "姓名不能为空"
					}
				},
				{
					prop: "nickName",
					label: "昵称",
					span: 12,
					component: {
						name: "el-input",
						props: {
							placeholder: "请填写昵称"
						}
					},
					rules: {
						required: true,
						message: "昵称不能为空"
					}
				},
				{
					prop: "username",
					label: "用户名",
					span: 12,
					component: {
						name: "el-input",
						props: {
							placeholder: "请填写用户名"
						}
					},
					rules: [
						{
							required: true,
							message: "用户名不能为空"
						}
					]
				},
				{
					prop: "password",
					label: "密码",
					span: 12,
					component: {
						name: "el-input",
						props: {
							placeholder: "请填写密码",
							type: "password"
						}
					},
					rules: [
						{
							min: 6,
							max: 16,
							message: "密码长度在 6 到 16 个字符"
						}
					]
				},
				{
					prop: "roleIdList",
					label: "角色",
					span: 24,
					value: [],
					component: {
						name: "cl-role-select",
						props: {
							props: {
								"multiple-limit": 3
							}
						}
					},
					rules: {
						required: true,
						message: "角色不能为空"
					}
				},
				{
					prop: "phone",
					label: "手机号码",
					span: 12,
					component: {
						name: "el-input",
						props: {
							placeholder: "请填写手机号码"
						}
					}
				},
				{
					prop: "email",
					label: "邮箱",
					span: 12,
					component: {
						name: "el-input",
						props: {
							placeholder: "请填写邮箱"
						}
					}
				},
				{
					prop: "remark",
					label: "备注",
					span: 24,
					component: {
						name: "el-input",
						props: {
							placeholder: "请填写备注",
							type: "textarea",
							rows: 4
						}
					}
				},
				{
					prop: "status",
					label: "状态",
					value: 1,
					component: {
						name: "el-radio-group",
						options: [
							{
								label: "开启",
								value: 1
							},
							{
								label: "关闭",
								value: 0
							}
						]
					}
				}
			]
		});

		return {
			table,
			upsert,
			setRefs,
			onLoad,
			refresh,
			onRefresh,
			onUpsertSubmit
		};
	}
});
</script>

<style lang="scss">
html,
body,
#app,
.demo {
	height: 100%;
	overflow: hidden;
}

* {
	padding: 0;
	margin: 0;
}
</style>
