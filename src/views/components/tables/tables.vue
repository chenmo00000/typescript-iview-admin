<template>
  <div>
    <Card>
      <tables
        ref="tables"
        editable
        searchable
        search-place="top"
        v-model="tableData"
        :columns="columns"
        @on-delete="handleDelete"
      />
      <Button style="margin: 10px 0;" type="primary" @click="exportExcel"
        >导出为Csv文件</Button
      >
    </Card>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from "vue-property-decorator";
import Tables from "_c/tables";
import { getTableData } from "@/api/data";

@Component({
  components: {
    Tables
  }
})
export default class TablesPage extends Vue {
  name = "tables_page";

  //data
  columns = [
    { title: "Name", key: "name", sortable: true },
    { title: "Email", key: "email", editable: true },
    { title: "Create-Time", key: "createTime" },
    {
      title: "Handle",
      key: "handle",
      options: ["delete"],
      button: [
        (h, params, vm) => {
          return h(
            "Poptip",
            {
              props: {
                confirm: true,
                title: "你确定要删除吗?"
              },
              on: {
                "on-ok": () => {
                  vm.$emit("on-delete", params);
                  vm.$emit(
                    "input",
                    params.tableData.filter(
                      (item, index) => index !== params.row.initRowIndex
                    )
                  );
                }
              }
            },
            [h("Button", "自定义删除")]
          );
        }
      ]
    }
  ];
  tableData = [];

  handleDelete(params) {
    console.log(params);
  }

  exportExcel() {
    (this.$refs.tables as Tables).exportCsv({
      filename: `table-${new Date().valueOf()}.csv`
    });
  }

  mounted() {
    getTableData().then(res => {
      this.tableData = res.data;
    });
  }
}
</script>
