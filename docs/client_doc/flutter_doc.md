> [点击前往Dart package](https://pub.flutter-io.cn/documentation/flutter_openim_sdk/latest/)查看详情

# 初始化与登录



## initSDK

初始化OpenIM SDK,设置SDK网络连接地址以及本地数据存放目录等。

```
OpenIM.iMManager.initSDK(
      //参考IMPlatform类
      platform: Platform.isAndroid ? IMPlatform.android : IMPlatform.ios,
      ipApi: '',
      ipWs: '',
      dbPath: '',
      listener: OnInitSDKListener(
        connecting: () {
          // 正在连接到服务器
        },
        connectFailed: (code, error) {
          // 连接服务器失败
        },
        connectSuccess: () {
          // 已经成功连接到服务器
        },
        kickedOffline: () {
          // 当前用户被踢下线
        },
        userSigExpired: () {
          // 登录票据已经过期
        },
        selfInfoUpdated: (info) {
          // 当前用户的资料发生了更新
        },
      ),
    );
```

- 参数说明：

| 名称     | 类型              | 描述                                                         |
| -------- | ----------------- | ------------------------------------------------------------ |
| platform | int               | 平台类型：IMPlatform类                                       |
| ipApi    | String            | SDK的API接口地址。如：http:xxx:10000                         |
| ipWs     | String            | SDK的web socket地址。如： ws:xxx:17778                       |
| dbPath   | String            | 数据存储路径。如：var apath =(await getApplicationDocumentsDirectory()).path |
| listener | OnInitSDKListener | SDK初始化监听                                                |

<font color='red'> 注：在创建图片，语音，视频，文件等需要路径参数的消息体时，如果选择的是非全路径方法如：createSoundMessage（全路径方法为：createSoundMessageFromFullPath），需要将文件自行拷贝到<font color='blue'>dbPath</font>目录下，如果此时文件路径为 apath+"/sound/a.mp3"，则参数path的值为：/sound/a.mp3。如果选择的全路径方法，路径为你文件的实际路径不需要再拷贝。 </font>

- OnInitSDKListener


| 事件回调          | 事件描述                 | 推荐操作                                                     |
| ----------------- | ------------------------ | ------------------------------------------------------------ |
| onConnecting      | 正在连接到服务器         | 适合在 UI 上展示“正在连接”状态。                             |
| onConnectSuccess  | 连接服务器失败           | -                                                            |
| onConnectFailed   | 已经成功连接到服务器     | 可以提示用户当前网络连接不可用                               |
| onKickedOffline   | 当前用户被踢下线         | 此时可以 UI 提示用户“您已经在其他端登录了当前账号，是否重新登录？” |
| onUserSigExpired  | 登录票据已经过期         | 请使用新签发的 UserSig 进行登录。                            |
| onSelfInfoUpdated | 当前用户的资料发生了更新 | 可以在 UI 上更新自己的头像和昵称。                           |

- 返回值说明：

initSDK方法返回Future对象

```
futrue.then((value) => '初始化成功').catchError((e)=>'初始化失败')
```



## login

使用用户ID(uid)和token登录，uid来自于自身业务服务器，token需要业务服务器根据secret向OpenIM服务端交换获取。

```
 OpenIM.iMManager.login(uid: uid, token: token);
```

- 参数说明

| 名称  | 类型   | 描述      |
| ----- | ------ | --------- |
| uid   | String | 用户ID    |
| token | String | 用户token |

- 返回值说明：

login方法返回Future对象

```
futrue.then((value) => '登录成功').catchError((e)=>'登录失败')
```



## logout

登出OpenIM，通常在切换账号的时候调用，清除登录态以及内存中的所有数据。

```
OpenIM.iMManager.logout();
```

- 返回值说明：

logout方法返回Future对象

```
futrue.then((value) => '登出成功').catchError((e)=>'登出失败')
```



## getLoginStatus

获取当前用户登录状态。

```
OpenIM.iMManager.getLoginStatus();
```

- 返回值说明：

getLoginStatus方法返回Future对象

```
futrue.then((value){
	if(value == 101){
		// 登录成功
	}
})
```



## getLoginUid

获取当前登录用户ID。

```
var uid = await OpenIM.iMManager.getLoginUid()
```



## getLoginUserInfo

获取当前登录用户的信息

```
var userInfo = await OpenIM.iMManager.getLoginUserInfo();
```



## setSelfInfo

修改当前登录用户信息

```
var result = await OpenIM.iMManager.setSelfInfo(info)
```

参数说明：

| 名称 | 类型     | 描述         |
| ---- | -------- | ------------ |
| info | UserInfo | 用户信息对象 |



## unInitSDK

取消初始化，一般用于在初始化后需要重新初始化

```
OpenIM.iMManager.unInitSDK();
```



# 用户资料



## getUsersInfo

根据用户ID批量获取用户信息

```
List<UserInfo> list = await OpenIM.iMManager.getUsersInfo(uidList);
```

- 参数说明：

| 名称    | 类型           | 描述       |
| ------- | -------------- | ---------- |
| uidList | List< String > | 用户ID集合 |

- 返回值说明：

| 类型                    | 描述         |
| ----------------------- | ------------ |
| Future<List< UserInfo>> | 用户信息列表 |



# 消息



## createTextMessage

创建一条文字消息。

```
Message message = await OpenIM.iMManager.messageManager.createTextMessage(  text: '这里是消息内容',)
```

- 参数说明：

| 名称 | 类型   | 描述     |
| ---- | ------ | -------- |
| text | String | 消息内容 |

返回值说明：

| 类型             | 描述     |
| ---------------- | -------- |
| Future< Message> | 消息对象 |



## createTextAtMessage

创建一条@消息

```
Message message = await OpenIM.iMManager.messageManager.createTextAtMessage(   text: '消息内容',   atUidList: [uid1,uid2,...],);
```

- 参数说明：

| 名称      | 类型          | 描述             |
| --------- | ------------- | ---------------- |
| text      | String        | 消息内容         |
| atUidList | List< String> | 选择的用户id集合 |



## createImageMessage

创建图片消息

```
Message message = await OpenIM.iMManager.messageManager.createImageMessage(  imagePath: '图片的相对路径',)
```

- 参数说明：

| 名称      | 类型   | 描述           |
| --------- | ------ | -------------- |
| imagePath | String | 图片的相对路径 |

<font color='red'>
注：initSDK时传入了数据缓存路径，如路径：A，这时需要你将图片复制到A路径下后，如 A/pic/a.png路径，imagePath的值：“/pic/a.png”。
</font>



## createImageMessageFromFullPath

创建图片消息（绝对路径）

```
Message message = await OpenIM.iMManager.messageManager.createImageMessageFromFullPath(  imagePath: path,)
```

- 参数说明：

| 名称      | 类型   | 描述                     |
| --------- | ------ | ------------------------ |
| imagePath | String | 图片在设备上的的绝对路径 |

此方法不需要拷贝，<font color='blue'>推荐使用</font>。



## createSoundMessage

创建语音消息

```
Message message = await OpenIM.iMManager.messageManager.createSoundMessage(  soundPath: '相对路径',  duration: '语音时长，单位秒',)
```

- 参数说明：

| 名称      | 类型   | 描述             |
| --------- | ------ | ---------------- |
| soundPath | String | 相对路径         |
| duration  | int    | 语音时长，单位秒 |

<font color='red'>注：initSDK时传入了数据缓存路径，如路径：A，这时需要你将语音文件复制到A路径下后，如 A/sound/a.m4a路径，soundPath的值：“/sound/a.m4a”</font>。



## createSoundMessageFromFullPath

创建语音消息（绝对路径）

```
Message message = await OpenIM.iMManager.messageManager.createSoundMessageFromFullPath(  soundPath: '在设备上的的实际路径',  duration: '语音时长，单位秒',)
```

- 参数说明：

| 名称      | 类型   | 描述                 |
| --------- | ------ | -------------------- |
| soundPath | String | 在设备上的的绝对路径 |
| duration  | int    | 语音时长，单位秒     |

此方法不需要拷贝，<font color='blue'>推荐使用</font>。



## createVideoMessage

创建视频消息

```
Message message = await OpenIM.iMManager.messageManager.createVideoMessage(        videoPath: '相对路径',        videoType: '文件的mineType',        duration: '视频时长，单位秒',        snapshotPath: '视频的缩略图',    )
```

- 参数说明：

| 名称         | 类型   | 描述             |
| ------------ | ------ | ---------------- |
| videoPath    | String | 相对路径         |
| videoType    | String | 文件的mineType   |
| duration     | int    | 视频时长，单位秒 |
| snapshotPath | String | 视频的缩略图     |

<font color='red'>注：initSDK时传入了数据缓存路径，如路径：A，这时需要你将视频文件复制到A路径下后，如 A/video/a.mp4路径，videoPath的值：“/video/a.mp4”</font>



## createVideoMessageFromFullPath

创建视频消息（绝对路径） 

```
Message message = await OpenIM.iMManager.messageManager.createVideoMessageFromFullPath(    videoPath: '在设备上的的实际路径',    videoType: '文件的mineType',    duration: '视频时长，单位秒',    snapshotPath: '视频的缩略图',)
```

- 参数说明：

| 名称         | 类型   | 描述                 |
| ------------ | ------ | -------------------- |
| videoPath    | String | 在设备上的的绝对路径 |
| videoType    | String | 文件的mineType       |
| duration     | int    | 视频时长，单位秒     |
| snapshotPath | String | 视频的缩略图         |

此方法不需要拷贝，<font color='blue'>推荐使用</font>。



## createFileMessage

创建文件消息

```
Message message = await OpenIM.iMManager.messageManager.createFileMessage(  filePath: '相对路径',  fileName: '文件名',);
```

- 参数说明：

| 名称     | 类型   | 描述     |
| -------- | ------ | -------- |
| filePath | String | 相对路径 |
| fileName | String | 文件名   |

<font color='red'>注：initSDK时传入了数据缓存路径，如路径：A，这时需要你将视频文件复制到A路径下后，如 A/file/a.txt路径，filePath的值：“/file/a.txt”</font>



## createFileMessageFromFullPath

创建文件消息（绝对路径）

```
Message message = await OpenIM.iMManager.messageManager.createFileMessageFromFullPath(  filePath: '在设备上的的绝对路径',  fileName: '文件名',)
```

- 参数说明：

| 名称     | 类型   | 描述                 |
| -------- | ------ | -------------------- |
| filePath | String | 在设备上的的绝对路径 |
| fileName | String | 文件名               |

此方法不需要拷贝，<font color='blue'>推荐使用</font>。



## createLocationMessage

创建位置消息

```
Message message = await OpenIM.iMManager.messageManager.createLocationMessage(  latitude: ’纬度‘,  longitude: ’经度‘,  description: ’描述信息，可以根据自己的需求传任何数据‘,)
```

- 参数说明：

| 名称        | 类型   | 描述                                   |
| ----------- | ------ | -------------------------------------- |
| latitude    | double | 纬度                                   |
| longitude   | double | 经度                                   |
| description | String | 描述信息，可以根据自己的需求传任何数据 |

description：在位置消息展示时，有位置名，位置描述，定位图片信息。推荐 description：{"title":"天府新谷","detail":"四川省高新区石羊街道府城大道西段399号","url":"https://apis.map.qq.com/ws/staticmap/v2/?center=%s&zoom=18&size=600*300&maptype=roadmap&markers=size:large|color:0xFFCCFF|label:k|%s&key=TMNBZ-3CGC6-C6SSL-EJA3B-E2P5Q-V7F6Q"}



## createQuoteMessage

创建引用消息

```
Message message = await OpenIM.iMManager.messageManager.createQuoteMessage(  text: '消息内容',  quoteMsg: '被引用的消息对象Message',)
```

- 参数说明：

| 名称     | 类型        | 描述                    |
| -------- | ----------- | ----------------------- |
| text     | String      | 消息内容                |
| quoteMsg | Message对象 | 被引用的消息对象Message |



## createCardMessage

创建名片消息

```
Message message = await OpenIM.iMManager.messageManager.createCardMessage(  data: '用户信息Map',)
```

- 参数说明：

| 名称 | 类型 | 描述                                                         |
| ---- | ---- | ------------------------------------------------------------ |
| data | Map  | 如{"uid": uid, 'name': name, 'icon': icon}，按需求自定义内容。 |



## createMergerMessage

创建合并消息

```
Message message = await OpenIM.iMManager.messageManager.createMergerMessage(  messageList: '被选中的消息',  title: '标题',  summaryList: '摘要',)
```

- 参数说明：

| 名称        | 类型           | 描述             |
| ----------- | -------------- | ---------------- |
| title       | String         | 标题             |
| summaryList | List< String>  | 摘要             |
| messageList | List< Message> | 被选中的消息集合 |



## createForwardMessage

创建转发消息

```
Message message = await OpenIM.iMManager.messageManager.createForwardMessage(  message: '被转发的Message',)
```

- 参数说明：

| 名称    | 类型    | 描述                    |
| ------- | ------- | ----------------------- |
| message | Message | 被选择转发的Message对象 |



## createCustomMessage

创建自定义消息

```
OpenIM.iMManager.messageManager.createCustomMessage(   data: '自定义数据',   extension: '自定义数据',   description: '自定义数据', )
```

- 参数说明：

| 名称        | 类型      | 描述           |
| ----------- | --------- | -------------- |
| data        | Uint8List | 自定义数据数组 |
| extension   | Uint8List | 自定义数据数组 |
| description | String    | 自定义数据     |



## sendMessage

发送消息

```
OpenIM.iMManager.messageManager    .sendMessage(      message: message,      userID: userId,      groupID: groupId,    )    .then((value){    	// 发送成功    })    .catchError((e){    	// 发送失败    })
```

- 参数说明：

| 名称           | 类型    | 描述               |
| -------------- | ------- | ------------------ |
| message        | Message | 创建的消息结构体   |
| userID         | String  | 单聊对象的用户id   |
| groupID        | String  | 群聊id             |
| onlineUserOnly | Bool    | 是否仅在线用户接收 |

注：如果一对一聊天 userID不能为空。如果群聊天groupID不能为空。如果消息发送成功执行then方法，发送失败执行catchError方法。



## typingStatusUpdate

提示用户正在输入

```
OpenIM.iMManager.messageManager.typingStatusUpdate(  userID: uid,  typing: true,)
```

- 参数说明：

| 名称   | 类型   | 描述                                |
| ------ | ------ | ----------------------------------- |
| userID | String | 单聊对象的用户id                    |
| typing | Bool   | typing：true正在输入，false停止输入 |

<font color='red'>注：单聊使用此功能，在收到的新消息回调里如果消息类型为typing消息且typing == 'yes'提示正在输入。typing=='no'取消提示</font>



## revokeMessage

撤回消息

```
OpenIM.iMManager.messageManager.revokeMessage(message: message)
```

- 参数说明：

| 名称    | 类型    | 描述           |
| ------- | ------- | -------------- |
| message | Message | 被撤回的消息体 |

<font color='red'>注：调用此方法会触发消息撤回回调，可以在回调里移除界面上的消息显示。也会触发新增消息回调，新增的消息类型为撤回消息类型，可以在界面显示一条xx撤回了一条消息</font>



## markC2CMessageAsRead

标记接收的消息为已读

```
OpenIM.iMManager.messageManager.markC2CMessageAsRead(  userID: userID,  messageIDList: [msgId1,msgId2,..],)
```

- 参数说明：

| 名称          | 类型          | 描述                     |
| ------------- | ------------- | ------------------------ |
| userID        | String        | 单聊对象的用户id         |
| messageIDList | List< String> | 被标记为已读消息的消息id |

<font color='red'>注：单聊使用此功能，调用此方法会触发对方的c2c消息已读回调，可以在回调里修改界面上的消息已读状态</font>



## getHistoryMessageList

获取聊天记录

```
OpenIM.iMManager.messageManager    .getHistoryMessageList(      userID: '单聊对象id',      groupID: '群聊群id',      count: '获取的条数',      startMsg:'从哪一个消息开始'    )
```

- 参数说明：

| 名称     | 类型    | 描述             |
| -------- | ------- | ---------------- |
| userID   | String  | 单聊对象的用户id |
| groupID  | String  | 群聊id           |
| count    | int     | 拉取的条数       |
| startMsg | Message | 从哪一个消息开始 |

<font color='red'>注：startMsg：如第一次拉取20条记录 startMsg=null && count=20 得到List< Message> list；下一次拉取消息记录参数：startMsg=list.first && count =20；以此内推，startMsg始终为list的第一条。</font>



## addAdvancedMsgListener

添加消息监听

```
// 创建监听var listener = OnAdvancedMsgListener(  recvMessageRevoked: (msgId){  		// 消息被撤回回调  	  var revokedMsg = Message(clientMsgID: msgId);      messageList.remove(revokedMsg);  },  recvC2CReadReceipt: (List<HaveReadInfo> list){  		// 消息已读回执  		 messageList.forEach((e) {  		 	// 获取当前聊天窗口的已读回执         var info = list.firstWhere((element) => element.uid == uid);        // 标记消息列表里对应的消息为已读状态        if (info.msgIDList?.contains(e.clientMsgID) == true) {          e.isRead = true;        }      });  },  recvNewMessage: (msg){  	// 如果是当前窗口的消息      if (isCurrentChat(message)) {        // 正在输入消息        if (message.contentType == MessageType.typing) {         //         } else {      		// 新增消息          if (!messageList.contains(message)) {            messageList.add(message);          }        }      }  },)// 添加监听OpenIM.iMManager.messageManager.addAdvancedMsgListener(listener);
```

| 事件回调           | 事件描述       | 推荐操作         |
| ------------------ | -------------- | ---------------- |
| recvMessageRevoked | 消息成功撤回   | 从界面移除消息   |
| recvC2CReadReceipt | 消息被阅读回执 | 将消息标记为已读 |
| recvNewMessage     | 收到新消息     | 界面添加新消息   |



## removeAdvancedMsgListener

移除消息监听

```
OpenIM.iMManager.messageManager.removeAdvancedMsgListener(listener);
```

- 参数说明：

| 名称     | 类型                      | 描述                       |
| -------- | ------------------------- | -------------------------- |
| listener | OnAdvancedMsgListener对象 | listener为3.22中创建的实例 |



## setMsgSendProgressListener

消息发送进度监听，主要用途：图片，视频，文件等上传进度监听。

```
OpenIM.iMManager.messageManager.setMsgSendProgressListener(OnMsgSendProgressListener(  progressCallback: (String msgId, int progress){  	// 根据消息id（clientMsgID），判断图片等上传进度  },))
```



## deleteMessageFromLocalStorage

删除本地消息。

```
OpenIM.iMManager.messageManager.deleteMessageFromLocalStorage(message);
```

- 参数说明：

| 名称    | 类型    | 描述             |
| ------- | ------- | ---------------- |
| message | Message | 被删除的消息对象 |



## insertSingleMessageToLocalStorage

插入一条消息到本地。

```
OpenIM.iMManager.messageManager.insertSingleMessageToLocalStorage(	userID: '接收者ID',	message: '消息结构体',	sender: '发送者ID')
```

- 参数说明：

| 名称     | 类型    | 描述       |
| -------- | ------- | ---------- |
| recevier | String  | 接收者ID   |
| sender   | String  | 发送者ID   |
| message  | Message | 消息结构体 |



## findMessages

根据消息ID查找本地消息。

```
OpenIM.iMManager.messageManager.findMessages(messageIDList: [id1,di2])
```

- 参数说明：

| 名称          | 类型          | 描述                      |
| ------------- | ------------- | ------------------------- |
| messageIDList | List< String> | 消息id（clientMsgID）集合 |



# 会话



## getAllConversationList

拉取当前所有的会话记录

```
List<ConversationInfo> list = await OpenIM.iMManager.conversationManager.getAllConversationList()
```



## getOneConversation

根据用户ID或群聊ID和session类型获取单个会话。

```
ConversationInfo info = await OpenIM.iMManager.conversationManager.getSingleConversation(		sourceID: sourceID, 		sessionType: sessionType,)
```

- 参数说明：

| 名称        | 类型   | 描述                                                         |
| ----------- | ------ | ------------------------------------------------------------ |
| sourceID    | String | 如果是单聊其值为单聊对象的userId；如果是群聊其值为群的groupId |
| sessionType | String | 如果是单聊sessionType=1；群聊sessionType=2                   |



## getMultipleConversation

根据会话ID获取多个会话。

```
List<ConversationInfo> list  = await OpenIM.iMManager.conversationManager.getMultipleConversation(		conversationIDList: conversationIDList);
```

- 参数说明：

| 名称               | 类型          | 描述         |
| ------------------ | ------------- | ------------ |
| conversationIDList | List< String> | 会话的id集合 |



## deleteConversation

删除会话，执行此操作会触发会话记录发生改变回调。

```
OpenIM.iMManager.conversationManager.deleteConversation(  conversationID: conversationID,);
```

- 参数说明：

| 名称           | 类型   | 描述           |
| -------------- | ------ | -------------- |
| conversationID | String | 被删除的会话id |



##  setConversationDraft

设置草稿，执行此操作会触发会话记录发生改变回调。

```
OpenIM.iMManager.conversationManager.setConversationDraft(  conversationID: conversationID,  draftText: draftText,);
```

- 参数说明：

| 名称           | 类型   | 描述                 |
| -------------- | ------ | -------------------- |
| draftText      | String | 未完成发送的消息内容 |
| conversationID | String | 会话id               |



## pinConversation

置顶会话，执行此操作会触发会话记录发生改变回调。

```
OpenIM.iMManager.conversationManager.pinConversation(  conversationID: conversationID,  isPinned: true,);
```

- 参数说明：

| 名称           | 类型   | 描述                        |
| -------------- | ------ | --------------------------- |
| isPinned       | bool   | true：置顶，false：取消置顶 |
| conversationID | String | 会话id                      |

注：ConversationInfo对象里的isPinned字段，isPinned==1代表置顶



## markSingleMessageHasRead

标记单聊会话消息为已读。

```
OpenIM.iMManager.conversationManager.markSingleMessageHasRead(userID: userID)
```

- 参数说明：

| 名称   | 类型   | 描述       |
| ------ | ------ | ---------- |
| userID | String | 单聊用户id |



## markGroupMessageHasRead

标记群聊会话消息已读。

```
OpenIM.iMManager.conversationManager.markGroupMessageHasRead(groupID: groupID)
```

- 参数说明：

| 名称    | 类型   | 描述   |
| ------- | ------ | ------ |
| groupID | String | 群组id |



## getTotalUnreadMsgCount

获取消息总未读。

```
int count = await OpenIM.iMManager.conversationManager.getTotalUnreadMsgCount();
```



## setConversationListener

设置会话监听器，会话记录发生改变时回调。

```
OpenIM.iMManager.conversationManager.setConversationListener(OnConversationListener(  conversationChanged: (list){  	// 会话记录改变  },  newConversation: (list){  	// 新增会话  },  totalUnreadMsgCountChanged: (count){  	// 未读消息总数改变  },  syncServerFailed: () {},  syncServerFinish: () {},  syncServerStart: () {},));
```

| 事件回调                   | 事件描述             | 推荐操作       |
| -------------------------- | -------------------- | -------------- |
| conversationChanged        | 会话记录发生改变     | 刷新会话列表   |
| newConversation            | 有新的会话被添加     | 刷新会话列表   |
| totalUnreadMsgCountChanged | 未读消息总数发生改变 | 刷新消息未读数 |



# 好友关系

## getFriendList

获取好友列表。

```
List<UserInfo> list = await OpenIM.iMManager.friendshipManager.getFriendList();
```

如果需要做联系人A-Z列表效果，推荐使用方法<font color='blue'>getFriendListMap</font>, 该方法返回值为Future<List< dynamic>> ，然后再将list转换为你自己的List Object，如：

```
OpenIM.iMManager.friendshipManager.getFriendListMap()        .then((list) => list.map((e) => ContactsInfo.fromJson(e)).toList());
```



##  getFriendsInfo

获取好友信息。

```
List<UserInfo> list = await OpenIM.iMManager.friendshipManager.getFriendsInfo(uidList: uidList)
```

- 参数说明：

| 名称    | 类型          | 描述         |
| ------- | ------------- | ------------ |
| uidList | List< String> | 用户的ID集合 |



## setFriendInfo

设置好友备注信息。

```
OpenIM.iMManager.friendshipManager.setFriendInfo(uid: uid, comment: '备注')
```

- 参数说明：

| 名称    | 类型   | 描述     |
| ------- | ------ | -------- |
| uid     | String | 用户的ID |
| comment | String | 备注     |



## checkFriend

检查与用户间是否有好友关系。

```
List<UserInfo> list = await OpenIM.iMManager.friendshipManager.checkFriend([id1,id2, ...]);
```

- 参数说明：

| 名称    | 类型          | 描述         |
| ------- | ------------- | ------------ |
| uidList | List< String> | 用户的ID集合 |

根据类UserInfo的flag字段判断。flag == 1：是好友关系，其他非好友关系。



## deleteFromFriendList

从好友列表中删除用户。

```
OpenIM.iMManager.friendshipManager.deleteFromFriendList(uid: uid);
```

- 参数说明：

| 名称 | 类型   | 描述     |
| ---- | ------ | -------- |
| uid  | String | 用户的ID |

返回Future，执行then方法为删除成功，执行cathError方法为删除失败。



## addFriend

发起添加好友申请。

```
OpenIM.iMManager.friendshipManager.addFriend(uid: uid, reason: reason);
```

- 参数说明：

| 名称   | 类型   | 描述     |
| ------ | ------ | -------- |
| uid    | String | 用户的ID |
| reason | String | 添加说明 |

返回Future，执行then方法为添加成功，执行cathError方法为添加失败。



## getFriendApplicationList

获取好友请求列表。

```
List<UserInfo> list = await OpenIM.iMManager.friendshipManager.getFriendApplicationList();
```

类UserInfo的flag字段：flag == 0：等待处理；flag == 1：已添加；flag == -1：已拒绝。

<font color='red'>注：新朋友的红点数通过计算列表里flag == 0的item的数量。</font>



##  acceptFriendApplication

接受好友请求。

```
OpenIM.iMManager.friendshipManager.acceptFriendApplication(uid: uid)
```

uid：为好友申请列表返回的数据里UserInfo类的uid字段。

返回Future，执行then方法为操作成功，执行cathError方法为操作失败。



## refuseFriendApplication

拒绝好友请求。

```
OpenIM.iMManager.friendshipManager.refuseFriendApplication(uid: uid)
```

返回Future，执行then方法为操作成功，执行cathError方法为操作失败。



##  addToBlackList

将用户添加到黑名单。

```
OpenIM.iMManager.friendshipManager.addToBlackList(uid: uid)
```

返回Future，执行then方法为操作成功，执行cathError方法为操作失败。



## deleteFromBlackList

从黑名单移除用户。

```
OpenIM.iMManager.friendshipManager.deleteFromBlackList(uid: uid);
```

返回Future，执行then方法为操作成功，执行cathError方法为操作失败。



##  getBlackList

获取黑名单列表。

```
List<UserInfo> list = await OpenIM.iMManager.friendshipManager.getBlackList();
```

<font color='red'>注：如果好友被拉进黑名单，调用<font color='blue'>getFriendList 或 getFriendListMap</font>方法得到好友包含了黑名单的人，需要通过UserInfo类的isInBlackList字段筛选，如果isInBlackList == 1说明被拉入黑名单。</font>



## setFriendListener

设置好友监听器。

```
OpenIM.iMManager.friendshipManager.setFriendshipListener(OnFriendshipListener(  blackListAdd: (u){  	// 好友被加入黑名时单回调  },  blackListDeleted: (u){    // 好友从黑名单移除时回调  },  friendApplicationListAccept: (u){    // 发起的好友请求被接受时回调  },  friendApplicationListAdded: (u){  	// 我接受被人的发起的好友请求时回调  },  friendApplicationListDeleted: (u){  	// 删除好友请求时回调  },  friendApplicationListReject: (u){  	// 请求被拒绝回调  },  friendInfoChanged: (u){  	// 好友资料发生变化时回调  },  friendListAdded: (u){  	// 已添加好友回调  },  friendListDeleted: (u){  	// 好友被删除时回调  },));
```

| 事件回调                     | 事件描述                   | 推荐操作                           |
| ---------------------------- | -------------------------- | ---------------------------------- |
| blackListAdd                 | 好友被加入黑名             | 刷新好友列表或黑名单列表           |
| blackListDeleted             | 好友从黑名单移除           | 刷新好友列表或黑名单列表           |
| friendApplicationListAccept  | 发起的好友请求被接受       | 刷新好友请求列表                   |
| friendApplicationListAdded   | 我接受被人的发起的好友请求 | 刷新好友请求列表                   |
| friendApplicationListDeleted | 删除好友请求               | 刷新好友请求列表                   |
| friendApplicationListReject  | 请求被拒绝                 | 刷新好友请求列表                   |
| friendInfoChanged            | 好友资料发生变化           | 刷新好友列表，好友信息或黑名单列表 |
| friendListAdded              | 已成为好友                 | 刷新好友列表                       |
| friendListDeleted            | 好友被删除                 | 刷新好友列表                       |



# 群关系



## createGroup

创建一个群聊，并指定群信息以及群成员。

```
OpenIM.iMManager.groupManager.createGroup( groupName: groupName,  notification: notification,  introduction: introduction,  faceUrl: faceUrl,  list: roles,)
```

- 参数说明：

| 名称         | 类型                   | 描述                                                         |
| ------------ | ---------------------- | ------------------------------------------------------------ |
| groupName    | String                 | 群名                                                         |
| notification | String                 | 群公告                                                       |
| introduction | String                 | 群介绍                                                       |
| faceUrl      | String                 | 群icon                                                       |
| list         | List< GroupMemberRole> | 在发起群聊时选择的群成员列表。<br />GroupMemberRole类字段说明：setRole：0:普通成员 2:管理员；uid：成员的uid。 |



## getGroupsInfo

批量获取群组信息。

```
List<GroupInfo> list = await OpenIM.iMManager.groupManager.getGroupsInfo(gidList: [gid1,gid2,...]);
```

- 参数说明：

| 名称    | 类型          | 描述       |
| ------- | ------------- | ---------- |
| gidList | List< String> | 群组id集合 |



##  setGroupInfo

设置、更新群聊信息。

```
OpenIM.iMManager.groupManager.setGroupInfo( 	groupName: groupName,  notification: notification,  introduction: introduction,  faceUrl: faceUrl, )
```

返回Future，执行then方法为操作成功，执行cathError方法为操作失败。



## getJoinedGroupList

获取已加入的群列表。

```
List<GroupInfo> list = await OpenIM.iMManager.groupManager.getJoinedGroupList();
```

如果类GroupInfo的ownerId字段的值跟当前用户的uid一致，则当前用户就是群的发起者。否则是参与者。



## getGroupMemberList

获取群成员列表。

GroupMembersList类的nextSeq字段：下一页的开始index。data字段：群成员。

```
GroupMembersList value = await OpenIM.iMManager.groupManager.getGroupMemberList(	groupId: gid,	filter:0,	next:0,)
```

- 参数说明：

| 名称    | 类型   | 描述                                                         |
| ------- | ------ | ------------------------------------------------------------ |
| groupId | String | 群组id                                                       |
| filter  | int    | 过滤成员，0不过滤，1群的创建者，2管理员；默认值0             |
| next    | int    | 分页，从哪一条开始获取，默认值0。参照GroupMembersList的nextSeq字段的值。 |



## getGroupMembersInfo

批量获取群成员信息。

```
List<GroupMembersInfo> list = await OpenIM.iMManager.groupManager.getGroupMembersInfo(	groupId: groupId, 	uidList: uidList)
```

- 参数说明：

| 名称    | 类型          | 描述         |
| ------- | ------------- | ------------ |
| groupId | String        | 群组id       |
| uidList | List< String> | 群成员ID集合 |



## joinGroup

申请加入群聊。

```
OpenIM.iMManager.groupManager.joinGroup(gid: gid, reason: null)
```

- 参数说明：

| 名称   | 类型   | 描述         |
| ------ | ------ | ------------ |
| gid    | String | 群组id       |
| reason | String | 请求验证信息 |

返回Future，执行then方法为操作成功，执行cathError方法为操作失败。



## getGroupApplicationList

获取加入群聊申请列表。

```
GroupApplicationList list = await OpenIM.iMManager.groupManager.getGroupApplicationList()
```

- GroupApplicationList字段说明：

| 名称  | 类型                        | 描述     |
| ----- | --------------------------- | -------- |
| count | int                         | 未处理数 |
| user  | List< GroupApplicationInfo> | 申请列表 |



## acceptGroupApplication

同意入群请求。

```
OpenIM.iMManager.groupManager.acceptGroupApplication(info: info, reason: reason)
```

- 参数说明：

| 名称   | 类型                 | 描述                  |
| ------ | -------------------- | --------------------- |
| info   | GroupApplicationInfo | 取6.8里申请列表的item |
| reason | String               | 同意原因              |

返回Future，执行then方法为操作成功，执行cathError方法为操作失败。



## refuseGroupApplication

拒绝入群请求。

```
OpenIM.iMManager.groupManager.refuseGroupApplication(info: info, reason: reason)
```

- 参数说明：

| 名称   | 类型                 | 描述                  |
| ------ | -------------------- | --------------------- |
| info   | GroupApplicationInfo | 取6.8里申请列表的item |
| reason | String               | 拒绝入群原因          |

返回Future，执行then方法为操作成功，执行cathError方法为操作失败。



## inviteUserToGroup

邀请用户加入群组(可批量)。

```
OpenIM.iMManager.groupManager.inviteUserToGroup(  groupId: gid,  uidList: uidList,  reason: reason,);
```

- 参数说明：

| 名称    | 类型          | 描述             |
| ------- | ------------- | ---------------- |
| groupId | String        | 群聊ID           |
| uidList | List< String> | 邀请用户的id集合 |
| reason  | String        | 请求验证信息     |

返回Future，执行then方法为操作成功，执行cathError方法为操作失败。



## kickGroupMember

踢出群聊(可批量)。

```
OpenIM.iMManager.groupManager.kickGroupMember(  groupId: gid,  uidList: uidList,  reason: reason,);
```

- 参数说明：

| 名称    | 类型          | 描述           |
| ------- | ------------- | -------------- |
| groupId | String        | 群聊ID         |
| uidList | List< String> | 踢出用户ID集合 |
| reason  | String        | 踢出原因       |

返回Future，执行then方法为操作成功，执行cathError方法为操作失败。



##  quitGroup

退出群聊。

```
OpenIM.iMManager.groupManager.quitGroup(gid: gid)
```

返回Future，执行then方法为操作成功，执行cathError方法为操作失败。



## transferGroupOwner

转让群主。

```
OpenIM.iMManager.groupManager.transferGroupOwner(gid: gid, uid: uid)
```

- 参数说明：

| 名称 | 类型   | 描述          |
| ---- | ------ | ------------- |
| gid  | String | 群id          |
| uid  | String | 新的拥有者uid |

注：此方法只有群的发起者（拥有者）才有权限访问，管理员和普通成员无权限访问。如果是发起者群资料展示可显示群权限转移按钮。



##  setGroupListener

设置群组监听器。

```
OpenIM.iMManager.groupManager.setGroupListener(OnGroupListener(  applicationProcessed: (groupId,  opUser,  agreeOrReject, opReason){  	// 群申请被处理时回调  },  groupCreated: (groupId){  	// 群创建完成时回调  },  groupInfoChanged: (groupId,  info){  	// 群资料发生变化时回调  },  memberEnter: ( groupId, list){  	// 有人进群时回调  },  memberInvited: ( groupId,  opUser,  list){  	// 接受邀请时回调  },  memberKicked: ( groupId,  opUser,  list){  	// 成员被踢出时回调  },  memberLeave: ( groupId,  info){  	// 群成员退群时回调  },  receiveJoinApplication: ( groupId,  info,  opReason){  	// 收到入群申请  },))
```

| 事件回调               | 事件描述       | 推荐操作       |
| ---------------------- | -------------- | -------------- |
| applicationProcessed   | 群申请被处理   |                |
| groupCreated           | 群创建完成     |                |
| groupInfoChanged       | 群资料发生变化 | 刷新群资料     |
| memberEnter            | 进群           | 刷新群成员列表 |
| memberInvited          | 接受邀请       | 刷新群成员列表 |
| memberKicked           | 成员被踢出     | 刷新群成员列表 |
| memberLeave            | 群成员退群     | 刷新群成员列表 |
| receiveJoinApplication | 收到入群申请   |                |



# 数据对象结构

## 用户信息对象

```
class UserInfo {  String uid;  String? name;  String? icon;  int? gender;  String? mobile;  String? birth;  String? email;  String? ex;  String? comment;  int? isInBlackList;  String? reqMessage;  String? applyTime;  int? flag; }
```

| Field         | **Description**                                              |
| ------------- | ------------------------------------------------------------ |
| uid           | 用户id                                                       |
| name          | 用户名                                                       |
| icon          | 用户头像                                                     |
| gender        | 性别：1男，2女                                               |
| mobile        | 手机号                                                       |
| birth         | 生日                                                         |
| email         | 邮箱                                                         |
| ex            | 扩展字段                                                     |
| comment       | 备注                                                         |
| isInBlackList | 黑名单：1已拉入黑名单                                        |
| reqMessage    | 验证消息                                                     |
| applyTime     | 申请时间                                                     |
| flag          | 好友申请列表：0等待处理；1已同意；2已拒绝<br />好友关系：1已经是好友 |



##  消息对象

```
class Message {  String? clientMsgID;  String? serverMsgID;  int? createTime;  int? sendTime;  String? sendID;  String? recvID;  int? msgFrom;  /// [MessageType]  int? contentType;  int? platformID;  List<String>? forceList;  String? senderNickName;  String? senderFaceUrl;  String? groupID;  String? content;  int? seq;  bool? isRead;  /// [MessageStatus]  int? status;  String? remark;  dynamic ext;  /// [ConversationType]  int? sessionType;  PictureElem? pictureElem;  SoundElem? soundElem;  VideoElem? videoElem;  FileElem? fileElem;  AtElem? atElem;  LocationElem? locationElem;  CustomElem? customElem;  QuoteElem? quoteElem;  MergeElem? mergeElem;}class PictureElem {  String? sourcePath;  PictureInfo? sourcePicture;  PictureInfo? bigPicture;  PictureInfo? snapshotPicture;}class PictureInfo {  String? uuID;  String? type;  int? size;  int? width;  int? height;  String? url;}class SoundElem {  String? uuID;  String? soundPath;  String? sourceUrl;  int? dataSize;  int? duration;}class VideoElem {  String? videoPath;  String? videoUUID;  String? videoUrl;  String? videoType;  int? videoSize;  int? duration;  String? snapshotPath;  String? snapshotUUID;  int? snapshotSize;  String? snapshotUrl;  int? snapshotWidth;  int? snapshotHeight;}class FileElem {  String? filePath;  String? uuID;  String? sourceUrl;  String? fileName;  int? fileSize;}class AtElem {  String? text;  List<String>? atUserList;  bool? isAtSelf;}class LocationElem {  String? description;  double? longitude;  double? latitude;}class CustomElem {  Uint8List? data;  Uint8List? extension;  String? description;}class QuoteElem {  String? text;  Message? quoteMessage;}class MergeElem {  String? title;  List<String>? abstractList;  List<Message>? multiMessage;}
```



### Message

| Field          | Description                                                  |
| -------------- | ------------------------------------------------------------ |
| clientMsgID    | 消息唯一ID                                                   |
| serverMsgID    | 消息服务器ID，暂时不使用                                     |
| createTime     | 消息创建时间，单位纳秒                                       |
| sendTime       | 消息发送时间，单位纳秒                                       |
| sendID         | 发送者ID                                                     |
| recvID         | 接收者ID                                                     |
| msgFrom        | 标识消息是用户级别还是系统级别 100:用户 200:系统             |
| contentType    | 消息类型：<br/>101:文本消息<br/>102:图片消息<br/>103:语音消息<br/>104:视频消息<br/>105:文件消息<br/>106:@消息<br/>107:合并消息<br/>108:转发消息<br/>109:位置消息<br/>110:自定义消息<br/>111:撤回消息回执<br/>112:C2C已读回执<br/>113:正在输入状态 |
| platformID     | 平台类型 1:ios 2:android 3:windows 4:osx 5:web 6:mini 7:linux |
| forceList      | 强制推送列表(被@的用户)                                      |
| senderNickName | 发送者昵称                                                   |
| senderFaceUrl  | 发送者头像                                                   |
| groupID        | 群聊ID                                                       |
| content        | 消息内容                                                     |
| seq            | 消息唯一序列号                                               |
| isRead         | 是否已读                                                     |
| status         | 消息状态 1:发送中 2:发送成功 3:发送失败 4:已删除 5:已撤回    |
| remark         | 消息备注                                                     |
| sessionType    | 会话类型 1:单聊 2:群聊                                       |
| pictureElem    | 图片信息                                                     |
| soundElem      | 语音信息                                                     |
| videoElem      | 视频信息                                                     |
| fileElem       | 文件信息                                                     |
| atElem         | @信息                                                        |
| locationElem   | 位置信息                                                     |
| customElem     | 自定义信息                                                   |
| quoteElem      | 引用消息                                                     |
| mergeElem      | 合并信息                                                     |



### PictureElem

| Field           | Description  |
| --------------- | ------------ |
| sourcePath      | 本地资源地址 |
| sourcePicture   | 本地图片详情 |
| bigPicture      | 大图详情     |
| snapshotPicture | 缩略图详情   |



### PictureInfo

| Field  | Description |
| ------ | ----------- |
| uuid   | 唯一ID      |
| type   | 图片类型    |
| size   | 图片大小    |
| width  | 图片宽度    |
| height | 图片高度    |
| url    | 图片oss地址 |



### SoundElem

| Field     | Description  |
| --------- | ------------ |
| uuid      | 唯一ID       |
| soundPath | 本地资源地址 |
| sourceUrl | oss地址      |
| dataSize  | 音频大小     |
| duration  | 音频时长     |



### VideoElem

| Field          | Description      |
| -------------- | ---------------- |
| videoPath      | 视频本地资源地址 |
| videoUUID      | 视频唯一ID       |
| videoUrl       | 视频oss地址      |
| videoType      | 视频类型         |
| videoSize      | 视频大小         |
| duration       | 视频时长         |
| snapshotPath   | 视频快照本地地址 |
| snapshotUUID   | 视频快照唯一ID   |
| snapshotSize   | 视频快照大小     |
| snapshotUrl    | 视频快照oss地址  |
| snapshotWidth  | 视频快照宽度     |
| snapshotHeight | 视频快照高度     |



### FileElem

| Field     | Description      |
| --------- | ---------------- |
| filePath  | 文件本地资源地址 |
| uuid      | 唯一ID           |
| sourceUrl | oss地址          |
| fileName  | 文件名称         |
| fileSize  | 文件大小         |



### MergeElem

| Field        | Description  |
| ------------ | ------------ |
| title        | 合并消息标题 |
| abstractList | 摘要列表     |
| multiMessage | 合并消息列表 |



### AtElem

| Field      | Description |
| ---------- | ----------- |
| text       | 文本消息    |
| atUserList | @用户ID列表 |
| isAtSelf   | 是否@自己   |



### LocationElem

| Field       | Description |
| ----------- | ----------- |
| description | 描述        |
| longitude   | 经度        |
| latitude    | 纬度        |



### CustomElem

| Field       | Description            |
| ----------- | ---------------------- |
| data        | 自定义消息字节数据     |
| extension   | 自定义消息扩展字节数据 |
| description | 描述                   |



### QuoteElem

| Field        | Description |
| ------------ | ----------- |
| quoteMessage | 引用消息    |



## 会话对象

```
class ConversationInfo {
  String conversationID;
  /// [ConversationType]
  int? conversationType;
  String? userID;
  String? groupID;
  String? showName;
  String? faceUrl;
  int? recvMsgOpt;
  int? unreadCount;
  Message? latestMsg;
  int? latestMsgSendTime;
  String? draftText;
  int? draftTimestamp;

  ///  pinned value is 1
  dynamic isPinned;
 }
```

| Field             | Description                                                  |
| ----------------- | ------------------------------------------------------------ |
| conversationID    | 会话ID                                                       |
| conversationType  | 会话类型 1:单聊 2:群聊                                       |
| userID            | 会话对象用户ID                                               |
| groupID           | 会话群聊ID                                                   |
| showName          | 会话对象(用户或群聊)名称                                     |
| faceUrl           | 用户头像或群聊头像                                           |
| recvMsgOpt        | 接收消息选项：<br/>1:在线正常接收消息，离线时进行推送<br/>2:不会接收到消息<br/>3:在线正常接收消息，离线不会有推送 |
| unreadCount       | 未读消息数量                                                 |
| latestMsg         | 最后一条消息 [消息对象](http://1.14.194.38:13123/client_doc/uni_doc.html)json字符串 |
| latestMsgSendTime | 最后一条消息发送时间(ns)                                     |
| draftText         | 会话草稿                                                     |
| draftTimestamp    | 会话草稿设置时间                                             |
| isPinned          | 是否置顶，1置顶                                              |



## 已读消息回执

```
class HaveReadInfo {
  String? uid;
  List<String>? msgIDList;
  int? readTime;
  int? msgFrom;
  int? contentType;
  int? sessionType;
 }
```

| Field       | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| uid         | 单聊对象的ID                                                 |
| contentType | 消息类型： 101:文本消息 102:图片消息 103:语音消息 104:视频消息 105:文件消息 106:@消息 107:合并消息 108:转发消息 109:位置消息 110:自定义消息 111:撤回消息回执 112:C2C已读回执 113:正在输入状态 |
| msgFrom     | 标识消息是用户级别还是系统级别 100:用户 200:系统             |
| msgIDList   | 已读消息clientMsgID集合                                      |
| readTime    | 已读时间                                                     |
| sessionType | 会话类型 1:单聊 2:群聊                                       |



## 群组信息对象

```
class GroupInfo {
  String groupID;
  String? groupName;
  String? notification;
  String? introduction;
  String? faceUrl;
  String? ownerId;
  int? createTime;
  int? memberCount;
  }
```

| Field        | Description  |
| ------------ | ------------ |
| groupID      | 群组ID       |
| groupName    | 群组名称     |
| notification | 群公告       |
| introduction | 群介绍       |
| faceUrl      | 群头像       |
| ownerId      | 群主ID       |
| createTime   | 群组创建时间 |
| memberCount  | 群成员总数   |



## 群组成员信息

```
class GroupMembersInfo {
  String? groupID;
  String? userId;
  int? role;
  int? joinTime;
  String? nickName;
  String? faceUrl;
  dynamic ext;
 }
```

| Field    | Description                                |
| -------- | ------------------------------------------ |
| groupID  | 群id                                       |
| userId   | 用户id                                     |
| role     | 用户的角色，0:普通成员  1:发起者  2:管理员 |
| joinTime | 加入时间                                   |
| nickName | 群昵称                                     |
| faceUrl  | 头像                                       |
| ext      | 扩展字段                                   |



## 群申请信息

```
class GroupApplicationInfo {
  String? id;
  String? groupID;
  String? fromUserID;
  String? toUserID;
  int? flag; //INIT = 0, REFUSE = -1, AGREE = 1
  String? reqMsg;
  String? handledMsg;
  int? createTime;
  String? fromUserNickName;
  String? toUserNickName;
  String? fromUserFaceURL;
  String? toUserFaceURL;
  String? handledUser;
  int? type; //APPLICATION = 0, INVITE = 1
  int? handleStatus; //UNHANDLED = 0, BY_OTHER = 1, BY_SELF = 2
  int? handleResult; //REFUSE = 0, AGREE = 1
  }
```

| Field            | Description                               |
| ---------------- | ----------------------------------------- |
| groupID          | 群组ID                                    |
| fromUserID       | 申请用户的ID                              |
| toUserID         | 接收用户的ID                              |
| flag             | 0：未处理，1：拒绝，2：同意               |
| reqMsg           | 入群原因                                  |
| handledMsg       | 处理反馈                                  |
| createTime       | 申请事件                                  |
| fromUserNickName | 申请用户的昵称                            |
| toUserNickName   | 接收用户的昵称                            |
| fromUserFaceURL  | 申请用户的头像                            |
| toUserFaceURL    | 接收用户的头像                            |
| handledUser      | 处理人                                    |
| type             | 0：申请进群, 1：邀请进群                  |
| handleStatus     | 0：未处理, 1：被其他人处理, 2：被自己处理 |
| handleResult     | 0：拒绝，1：同意                          |
