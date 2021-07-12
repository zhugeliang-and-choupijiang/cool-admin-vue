<template>
	<div class="demo">
		<cl-crud @load="onLoad">
			<el-row>
				<cl-refresh-btn />
				<cl-add-btn />
				<cl-search-key field="name" :field-list="[{ label: '名称', value: 'name' }]" />
			</el-row>

			<el-row>
				<!-- 数据表格 -->
				<cl-table v-bind="table">
					<template #column-img="{ scope }">
						<img :src="scope.row.img" min-width="70" height="70" />
					</template>
				</cl-table>
			</el-row>

			<el-row>
				<cl-flex1 />
				<cl-pagination />
			</el-row>

			<demo-upsert />
		</cl-crud>
	</div>
</template>

<script lang="ts">
import { defineComponent, inject, reactive } from "vue";
import Upsert from "../components/brands/upsert.vue";
import { CrudLoad, RefreshOp, Table } from "cl-admin-crud-vue3/types";
import moment from "moment";

export default defineComponent({
	name: "brands",

	components: {
		"demo-upsert": Upsert
	},

	setup() {
		const service = inject<any>("service");
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

		// 刷新钩子
		function onRefresh(params: any, { next, render }: RefreshOp) {
			next(params).then((res: any) => {
				const list = res.map((e: any) => {
					e._enable = e.enable ? true : false;
					return e;
				});

				render(list, {
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
					showOverflowTooltip: true,
					minWidth: 100
				},
				{
					label: "品牌图",
					prop: "img",
					showOverflowTooltip: true,
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
					sortable: "custom",
					formatter: timeformat
				}
			]
		});

		return {
			table,
			onLoad,
			onRefresh
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
