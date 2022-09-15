# 文献管理相关

SaltDog提供了一些和文献管理相关的API，以方便插件扩展文献管理功能。
## 信息查询
### saltdog.library.listDir(dirID)
查询某文件夹下的条目和子文件夹信息
#### 参数说明
- dirID - 待查的文件夹ID
#### 返回值
- Promise\<IDirList\>  详见: [IDirList](zh-cn/api/types.md#IDirList)

## 列表展示
### saltdog.library.registerDisplayProvider((listData)=>listData)
增加文献列表中要展示的字段

默认情况下，SaltDog的文献管理页面仅展示文献标题，更多内容需要点击展示。为了满足多样化的需求，插件可以使用`registerDisplayProvider`实现展示其他自定义关键字。

### DisplayProvider函数使用
该Provider在列表展示之前被触发，传入一个对象`listData`,该函数再修改后需要将其**返回**。

**`DisplayProvider`支持异步函数**

```javascript
> listData
> {
    column:[],
    row:[
        {id:1,name:'dir1',type:'dir',customFields:{}},
        {id:1,name:'paper1',type:'item',itemType: "journalArticle",customFields:{}},
        ....
    ]
}
```
#### 各字段含义解释
为解释清楚`listData`含义，将采用一个小demo作为演示：增加期刊分区信息。
##### column
默认为空，记录了自定义列的信息，我们在这里需要增加"分区"列，因此向其中插入一个对象,变为：
```javascript
column:[{displayName:'分区',indexName:'region'}];
```
|字段名|含义|
|---|---|
|displayName|用户所见的列表标题栏名称|
|indexName|插件自定义的索引名，要和row.customFields中一一对应|
##### row
row的每一个对象都代表了展示的一个条目。
|字段名|含义|备注|
|---|---|---|
|id|当type=='dir'时，为dirID;当type=='item'时，为itemID|请勿修改|
|name|列表展示的条目名称或文件夹名称|请勿修改|
|type|条目类型，'item'或'dir'|请勿修改|
|itemType|当type=='item'时代表条目的Schema类型|请勿修改|
|customFields|插件新增的自定义字段|
|customFields[indexName].displayType|展示类型，目前仅支持留空或'tag'，留空为文本，'tag'则以徽标形式展示||
|customFields[indexName].text|展示文本内容||

插件需要修改`row.customFields`
比如如下代码将所有偶数位条目标为"SCI"
```javascript
saltdog.library.registerDisplayProvider((data) => {
        // 采用push而不是重新赋值！
        data.column.push({ displayName: '分区', indexName: 'region' });
        for (let i = 0; i < data.row.length; i++) {
            data.row[i].customFields = data.row[i].customFields || {};
            if (i % 2 == 0) {
                data.row[i].customFields['region'] = {
                    displayType: 'tag',
                    text: 'SCI',
                };
            } else {
                data.row[i].customFields['region'] = {
                    text: '暂无',
                };
            }
        }
        return data;
    });
```
经过修改后，上述的listData结构最终变为：
```javascript
> listData
> {
    column:[{displayName: "分区",indexName: "region"}],
    row:[
        {id:1,name:'dir1',type:'dir',customFields:{region: {text: '暂无'}}},
        {id:2,name:'paper1',type:'item',itemType: "journalArticle",customFields:{region: {displayType: 'tag', text: 'SCI'}}},
        ....
    ]
}
```
### 注意与提示
- `DisplayProvider`的回调函数一定要将`listData`返回，否则会造成列表展示为空。
- 在修改column和row内容时，请优先使用push等方法进行非覆盖修改。若有多个插件注册DisplayProvider，SaltDog会将其串行调用。也就是说，某插件可能被传递的`listData`是上一个插件修改过后的结果。因此覆盖赋值会导致之前插件的更改消失。
- 若需要某条目的具体信息，可以通过调用`saltdog.library.getItemInfo(itemID)`方法或直接查询SaltDog数据库。


