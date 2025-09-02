---
title: vue-print-nb
date: 2022-06-09 19:16:45
comments: true #是否可评论
tags: # 文章的标签
	- vue
	- 打印
categories: 
	- vue-print-nb打印
description: 基于Vue的页面打印功能。	  
top_img: https://pic.imgdb.cn/item/65674069c458853aef243189.jpg     #顶部图片
cover: https://pic.imgdb.cn/item/65719fb1c458853aef7d6ec2.jpg   #主页中显示的图片
---
vue-print-nb的github地址:   https://github.com/Power-kxLee/vue3-print-nb#print-the-entire-page

1.安装vue3-print-nb
```javascript
npm install vue3-print-nb
```

2.在main.ts中引入
```javascript
//引入
import print from 'vue3-print-nb'
//挂载
const app = createApp(App)
app.use(print)
```

3.Vue页面中
```javascript
<!-- 打印区域容器 -->
<div id="printBox">
<span>我就是被打印的内容</span>
<span>在#printBox 容器里的内容都会被打印噢</span>
</div>
 
<!-- 按钮绑定打印 -->
<button v-print="print">点击打开打印预览</button>	
```
4.参数配置项
```javascript
//这里是打印的配置项
const  print=ref({
        id: 'printBox',//这里的id就是上面我们的打印区域id，实现指哪打哪
        popTitle: '配置页眉标题', // 打印配置页上方的标题
        extraHead: '', // 最上方的头部文字，附加在head标签上的额外标签，使用逗号分割
        preview: false, // 是否启动预览模式，默认是false
        previewTitle: '预览的标题', // 打印预览的标题
        previewPrintBtnLabel: '预览结束，开始打印', // 打印预览的标题下方的按钮文本，点击可进入打印
        zIndex: 20002, // 预览窗口的z-index，默认是20002，最好比默认值更高
        previewBeforeOpenCallback() { console.log('正在加载预览窗口！'); }, // 预览窗口打开之前的callback
        previewOpenCallback() { console.log('已经加载完预览窗口，预览打开了！') }, // 预览窗口打开时的callback
        beforeOpenCallback() { console.log('开始打印之前！') }, // 开始打印之前的callback
        openCallback() { console.log('执行打印了！') }, // 调用打印时的callback
        closeCallback() { console.log('关闭了打印工具！') }, // 关闭打印的callback(无法区分确认or取消)
        clickMounted() { console.log('点击v-print绑定的按钮了！') },
      })

```

5.API参数
![](https://pic.imgdb.cn/item/65751ae6c458853aef204048.jpg)


问题:因为有些数据并不在前端、需要我们通过后端请求返回后再去渲染页面的？但是点击打印按钮的时候就会直接渲染出打印页面，我们没办法阻止。

尝试：在  beforeOpenCallback()(调用打印工具前的回调函数)，想去推后页面渲染将ajax请求设置为同步请求，但是仍然无法阻止打印页面的渲染。


解决方法！！！！：
这算是一个曲线救国的方式。
既然点击打印按钮我们没办法阻止打印页面的渲染。那么就想办法将点击打印按钮的事件退后。那如何推后在请求完成后又能自动触发打印呢？
我们可以将原本的打印按钮作为     假打印按钮(还原为普通按钮，按钮中不再带v-print="dataForm.printContent"的属性)      再打印的页面中做一个  真打印按钮（带有v-print="dataForm.printContent"的属性）
假打印按钮   主要是去查询数据，等待ajax完成后在去自动触发   真打印按钮  ，这样就可以推迟打印页面的渲染。

```javascript
      <div style="margin-top: 20px">
        <el-table
          :data="dataForm.dataList"
          style="width: 100%"
          :height="dataForm.tableSize"
          border
          header-align="center"
          align="center"
        >
          <el-table-column
            property="outputCode"
            align="center"
            :label="t('ui.InventoryOutput.outputCode')"
            width="150"
          >
            <template #default="scope">
              <el-button type="primary" @click="showDetail(scope.row)" link
                >{{ scope.row.outputCode }}
              </el-button>
            </template>
          </el-table-column>
          <el-table-column
            property="outputType"
            align="center"
            width="120"
            :label="t('ui.InventoryOutput.outputType')"
          />
          <el-table-column
            property="outputTime"
            align="center"
            :label="t('ui.InventoryOutput.outputTime')"
            width="160"
          />
          <el-table-column
            property="outputStatus"
            align="center"
            width="120"
            :label="t('ui.InventoryOutput.outputStatus')"
          >
            <template v-slot="scope">
              <el-tag type="danger">已出库 </el-tag>
            </template>
          </el-table-column>
          <el-table-column
            property="reason"
            align="center"
            width="150"
            :label="t('ui.InventoryOutput.reason&use')"
          />
          <el-table-column
            property="person"
            :label="t('ui.InventoryOutput.person')"
            align="center"
            width="100"
          />
          <el-table-column
            property="allQty"
            align="center"
            width="100"
            :label="t('ui.InventoryOutput.allQty')"
          />
          <el-table-column
            property="createUser"
            align="center"
            :label="t('ui.common.createUser')"
            width="120"
          />
          <el-table-column
            property="createTime"
            align="center"
            width="160"
            :label="t('ui.common.createTime')"
          />
          <el-table-column
            property="updateUser"
            align="center"
            :label="t('ui.common.updateUser')"
            width="120"
          />
          <el-table-column
            property="updateTime"
            align="center"
            width="160"
            :label="t('ui.common.updateTime')"
          />
          <el-table-column
            property="action"
            :label="t('ui.common.operation')"
            align="center"
            fixed="right"
            width="200"
          >
            <template #default="scope">
			重点1：表格中的按钮换为不带v-print属性的普通按钮！！！！！！！！！！！！！！！！！
              <el-button @click="toPrint(scope.row)" link type="primary"
                >打印
              </el-button>
              <el-button
                link
                type="primary"
                :icon="useRenderIcon(Delete)"
                @click="del(scope.row)"
              >
                {{ t("ui.common.delete") }}
              </el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
```



```javascript
      <div style="display: none">
	  重点2:在打印的内容中加真按钮 ！！！！！！！！！！！！
        <button
          id="btnPrint"
          type="button"
          value="按钮"
          style="display: none"
          v-print="dataForm.printContent"
        />
        <div id="printTest">
          <div style="page-break-after: always">
            <el-row>
              <div
                style="
                  margin: auto;
                  font-size: 30px;
                  text-align: center;
                  color: rgb(0, 0, 0);
                  font-weight: 600;
                "
              >
                装备调拨单
              </div>
            </el-row>
            <el-row>
              <el-col :span="24" style="font-size: 15px; color: #000000"
                >配发时间:
                &nbsp;&nbsp;&nbsp;&nbsp;年&nbsp;&nbsp;&nbsp;&nbsp;月&nbsp;&nbsp;&nbsp;&nbsp;日</el-col
              >
            </el-row>

            <el-row>
              <!--            打印的表格-->
              <el-col :span="22"
                ><table
                  style="
                    margin-top: 20px;
                    height: 45px;
                    font-family: Arial, sans-serif;
                    font-size: 14px;
                    border-collapse: collapse;
                    color: #454545;
                    table-layout: auto;
                    width: 100%;
                    text-align: center;
                    border-width: 1px;
                    border-style: solid;
                    border-color: #000000;
                  "
                  ref="printId"
                  id="printId"
                  cellspacing="0"
                  cellpadding="0"
                  border="0"
                >
                  <tbody>
                    <col style="width: 5%" />
                    <col style="width: 5%" />
                    <col style="width: 40%" />
                    <col style="width: 5%" />
                    <col style="width: 5%" />
                    <col style="width: 25%" />
                    <col style="width: 15%" />
                    <tr style="line-height: 23px">
                      <td
                        colspan="2"
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        调拨原因
                      </td>
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        {{ dataForm.printFrom.reason }}
                      </td>
                      <td
                        colspan="2"
                        style="
                          width: 30px;
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        调拨单号
                      </td>
                      <td
                        colspan="2"
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        {{ dataForm.printFrom.outputCode }}
                      </td>
                    </tr>

                    <tr style="line-height: 23px">
                      <td
                        colspan="2"
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        接收单位
                      </td>
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        {{}}
                      </td>
                      <td
                        colspan="2"
                        style="
                          width: 30px;
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        接收人
                      </td>
                      <td
                        colspan="2"
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        {{}}
                      </td>
                    </tr>
                    <tr style="line-height: 23px">
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        序号
                      </td>
                      <td
                        colspan="2"
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        装备名称
                      </td>
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        单位
                      </td>
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        数量
                      </td>
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        编号
                      </td>
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        附件
                      </td>
                    </tr>

                    <tr
                      v-for="(item, index) in dataForm.printFrom.printDataList"
                      :key="index"
                    >
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        {{ index + 1 }}
                      </td>
                      <td
                        colspan="2"
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                          text-align: left;
                        "
                      >
                        {{ item.name }}
                      </td>
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        {{ item.unitName }}
                      </td>
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        {{ item.outputQty }}
                      </td>
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        {{ item.content }}
                      </td>
                      <td
                        style="
                          border-width: 1px;
                          border-style: solid;
                          border-color: #000000;
                        "
                      >
                        {{}}
                      </td>
                    </tr>
                  </tbody>
                </table></el-col
              >
              <el-rol :span="1"> &nbsp; </el-rol>
              <el-col :span="1">
                <br />
                <h
                  >第<br />
                  一<br />
                  联<br />
                  <br />
                  调<br />
                  拨<br />
                  单<br />
                  位<br />
                  留<br />
                  存<br />
                </h>
              </el-col>
            </el-row>
            <br />
            <el-row>
              <el-col :span="8"> 调拨单位(盖章): </el-col>
              <el-col :span="8"> 审批领导: </el-col>
              <el-col :span="8"> 经办人: </el-col>
            </el-row>
          </div>
        </div>
      </div>

```



	  
	  

```javascript
	  //假打印按钮实现查询数据和自动触发真打按钮的和事件。
function toPrint(row) {
  dataForm.printFrom.tempRow = row;
  dataForm.printFrom.outputCode = dataForm.printFrom.tempRow.outputCode;
  dataForm.printFrom.reason = dataForm.printFrom.tempRow.reason;
  proxy.ajax({
    url: "/pla/inventoryOutput/queryPrintByOutputCode",
    method: "post",
    data: {
      outputCode: dataForm.printFrom.tempRow.outputCode
    },
    success(res) {
      dataForm.printFrom.printDataList = res;
      重点3. 等待页面渲染完成后再执行自动触发按钮！！！！！！！！！！！！nextTick的作用就是等待前面任务的页面渲染完成后再继续执行下去！！！
      proxy.$nextTick(() => {
        document.getElementById("btnPrint").click();
      });
    }
  });
}
```