#  Element-UI

`<el-container>`：外层容器。当子元素中包含 `<el-header>` 或 `<el-footer>` 时，全部子元素会垂直上下排列，否则会水平左右排列。

`<el-header>`：顶栏容器。

`<el-aside>`：侧边栏容器。

`<el-main>`：主要区域容器。

`<el-footer>`：底栏容器。

#### Tab选项卡

```js
<el-tabs v-model="activeName" @tab-click="handleClick">
    <el-tab-pane label="用户管理" name="first">用户管理</el-tab-pane>
    <el-tab-pane label="配置管理" name="second">配置管理</el-tab-pane>
    <el-tab-pane label="角色管理" name="third">角色管理</el-tab-pane>
    <el-tab-pane label="定时任务补偿" name="fourth">定时任务补偿</el-tab-pane>
  </el-tabs>
```

- el-tabs: 总标签
- el-tab-pane：选项卡标签 在pane中添加面板上的内容
  - label：选项卡标签名称
  - name：activeName用于选择哪个选项卡被选中

#### Table表格

当`el-table`元素中注入`data`对象数组后，在`el-table-column`中用`prop`属性来对应对象中的键名即可填入数据，用`label`属性来定义表格的列名。可以使用`width`属性来定义列宽。

#### Form表单

在 Form 组件中，每一个表单域由一个 Form-Item 组件构成，表单域中可以放置各种类型的表单控件，包括 Input、Select、Checkbox、Radio、Switch、DatePicker、TimePicker

#### Collapse折叠面板

```js
<el-collapse>
    <el-collapse-item title="监测井基本信息">
    	#一个折叠面板 title为标题
    </el-collapse-item>
	<el-collapse-item>
    	#一个折叠面板
    </el-collapse-item >
</el-collapse>
```

#### Select 选择器

```js
<el-select v-model="wellLevel" multiple collapse-tags placeholder="请选择控制级别" v-if="operateType != 'detail'">
       <el-option
           v-for="item in wellLevelList"
           :key="item.key"
           :label="item.label"
           :value="item.key">
        </el-option>
 </el-select>
```

- `v-model`的值为当前被选中的`el-option`的 value 属性值

- `multiple`属性即可启用多选，此时`v-model`的值为当前选中值所组成的数组

- 选中值会以 Tag 的形式展现，你也可以设置`collapse-tags`属性将它们合并为一段文字。

- `el-option`为下拉选项

- wellLevelList 为数组 元素 为key + label

  ```js
  wellLevelList: [
          {key: '国控', label: '国控'},
          {key: '省控', label: '省控'},
          {key: '市控', label: '市控'}
        ]
  ```

  

  