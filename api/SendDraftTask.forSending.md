# 发送邮件
## 页面操作方法：
- 新建邮件
- 点击发送

## native端业务逻辑猜想
- 把接收到数据保存到Message, MessageBody中
- 把接收到的数据保存到sqlite的Task表中，status为local，同时返回给客户端JSON-1和JSON-2
- 开始与邮件服务器同步，成功后Task表中的status变为remote
- 返回JSON-3
- 这里还有别的处理么？？？
- Task表中的status变为complete
- 向Task表中插入数据
- 发送邮件
- 向Message, MessageBody, Thread, ThreadCategory表中插入数据
    - ThreadId为`t:${MessageId}` 例如：t:CTL1UmXPu31ndj7L4sZVUHhd84FjP6tTT6SnUTTbv
    - ThreadCategory表中的value为Folder的id
- 更新ThreadCounts表中的数据
- Task表中的status变为complete
- 返回JSON-4

## Post Data
```json
{
	"type": "queue-task",
	"task": {
		"id": "local-3ae3616e-3ac9", // (客户端生成)任务id。规则：`local-${s4()}${s4()}-${s4()}`
		"aid": "9b3a80a0",
		"status": "local",
		"draft": {
			"id": "fwHwGQpYorWyZpF1hs5RmbbMynVmXKXZZvRVTuThq", // 草稿id
			"aid": "9b3a80a0",
			"metadata": [{
				"pluginId": "link-tracking", // 插件名
				"v": 2,
				"value": {
					"tracked": true,
					"links": [{ // 这里的链接是邮件签名中的链接。这里的redirect_url是由客户端生成的，域名是在package.json文件中设定的
						"url": "mailto:test@abc.com",
						"click_count": 0,
						"click_data": [],
						"redirect_url": "https://link.getmailspring.com/link/1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com/0?redirect=mailto:test@abc.com"
					}, {
						"url": "https://maps.google.com/?q=åäº¬åé¾è§ä¸å¤§è¡3",
						"click_count": 0,
						"click_data": [],
						"redirect_url": "https://link.getmailspring.com/link/1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com/1?redirect=https://maps.google.com/?q=%E5%8C%97%E4%BA%AC%E5%9B%9E%E9%BE%99%E8%A7%82%E4%B8%9C%E5%A4%A7%E8%A1%973"
					}]
				},
				"__cls": "PluginMetadata"
			}, {
				"pluginId": "open-tracking",
				"v": 2,
				"value": {
					"open_count": 0,
					"open_data": []
				},
				"__cls": "PluginMetadata"
			}, {
				"pluginId": "send-later",
				"v": 2,
				"value": {
					"expiration": null
				},
				"__cls": "PluginMetadata"
			}],
			"to": [{ // 收件人
				"id": "qzs0390@sina.com",
				"aid": "9b3a80a0",
				"name": "qzs0390@sina.com",
				"email": "qzs0390@sina.com",
				"refs": 4,
				"thirdPartyData": {},
				"__cls": "Contact"
			}],
			"cc": [],
			"bcc": [],
			"from": [{ // 发件人
				"id": "local-9b3a80a0-me",
				"aid": "9b3a80a0",
				"name": "haha pipi",
				"email": "quanzhansheng@msn.com",
				"thirdPartyData": {},
				"__cls": "Contact"
			}],
			"replyTo": [],
			"date": 1536217708,
			"body": "<div>吃点低热量的，要不然太胖了</div><br/><div><signature id=\"initial\"><table cellpadding=\"0\" cellspacing=\"0\"><tbody><tr><td style=\"vertical-align:top;width:1px\"></td><td><div>这是一个签名测试</div></td></tr><tr><td colspan=\"2\"><div style=\"font-size:0.9em;border-top:1px dashed gray;min-width:250px;max-width:400px;margin-top:10px;padding-top:4px\"><div><a style=\"margin-right:8px\" href=\"mailto:test@abc.com\">test@abc.com</a></div><div></div><div><a href=\"https://maps.google.com/?q=åäº¬åé¾è§ä¸å¤§è¡3\">北京回龙观东大街3</a></div></div></td></tr></tbody></table></signature></div>",
			"files": [],
			"unread": false, // 是否未读
			"events": [],
			"starred": false, // 是否有星标
			"threadId": "",
			"hMsgId": "1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com", // headerMessageId
			"subject": "下午好，晚上吃点啥", // 邮件标题
			"draft": true, // 是否为草稿
			"pristine": false,
			"v": 11, // 版本号，编辑的时候+1
			"folder": {
				"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa",
				"aid": "9b3a80a0",
				"role": "drafts",
				"path": "Drafts", // 文件夹路径
				"localStatus": { // 为了显示本地文件夹同步的状况（由mailsync计算得出）, 详细请参考<a href="SyncbackDraftTask.md" target="_blank">【SyncbackDraftTask.md】</a>
					"bodiesPresent": 5,
					"bodiesWanted": 0,
					"busy": false,
					"highestmodseq": 0,
					"lastCleanup": 1536216770,
					"lastDeep": 1536217394,
					"lastShallow": 1536217522,
					"syncedMinUID": 1,
					"uidnext": 7,
					"uidvalidity": 114,
					"uidvalidityResetCount": 0
				},
				"__cls": "Folder"
			},
			"__cls": "Message",
			"file_ids": [], // 附件id
			"object": "draft"
		},
		"headerMessageId": "1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com",
		"perRecipientBodies": { // 收件人展示的邮件正文。 self是给自己看的，其他的是给收件人看的。
			"self": "<div>吃点低热量的，要不然太胖了</div><br/><div><signature id=\"initial\"><table cellpadding=\"0\" cellspacing=\"0\"><tbody><tr><td style=\"vertical-align:top;width:1px\"></td><td><div>这是一个签名测试</div></td></tr><tr><td colspan=\"2\"><div style=\"font-size:0.9em;border-top:1px dashed gray;min-width:250px;max-width:400px;margin-top:10px;padding-top:4px\"><div><a style=\"margin-right:8px\" href=\"mailto:test@abc.com\">test@abc.com</a></div><div></div><div><a href=\"https://maps.google.com/?q=åäº¬åé¾è§ä¸å¤§è¡3\">北京回龙观东大街3</a></div></div></td></tr></tbody></table></signature></div>",
			"qzs0390@sina.com": "<div>吃点低热量的，要不然太胖了</div><br><div><signature id=\"initial\"><table cellpadding=\"0\" cellspacing=\"0\"><tbody><tr><td style=\"vertical-align:top;width:1px\"></td><td><div>这是一个签名测试</div></td></tr><tr><td colspan=\"2\"><div style=\"font-size:0.9em;border-top:1px dashed gray;min-width:250px;max-width:400px;margin-top:10px;padding-top:4px\"><div><a style=\"margin-right:8px\" href=\"https://link.getmailspring.com/link/1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com/0?redirect=mailto:test@abc.com&amp;recipient=cXpzMDM5MEBzaW5hLmNvbQ==\">test@abc.com</a></div><div></div><div><a href=\"https://link.getmailspring.com/link/1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com/1?redirect=https://maps.google.com/?q=%E5%8C%97%E4%BA%AC%E5%9B%9E%E9%BE%99%E8%A7%82%E4%B8%9C%E5%A4%A7%E8%A1%973&amp;recipient=cXpzMDM5MEBzaW5hLmNvbQ==\">北京回龙观东大街3</a></div></div></td></tr></tbody></table></signature></div><img class=\"mailspring-open\" alt=\"Open Tracking\" width=\"0\" height=\"0\" style=\"border:0; width:0; height:0;\" src=\"https://link.getmailspring.com/open/1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com?recipient=cXpzMDM5MEBzaW5hLmNvbQ==\">"
		},
		"__cls": "SendDraftTask" // 类名,用这个来区分要做的业务。
	}
}
```

## Response data
- JSON-1
```json
{
	"modelClass": "Message",
	"modelJSONs": [{
		"__cls": "Message",
		"_sa": 0, // （猜测）邮件送达时间，未送到为0，送达为unix时间戳
		"_suc": 0, // 是否成功发出 0:未发出，1:已发出
		"aid": "9b3a80a0",
		"bcc": [],
		"body": "<div>吃点低热量的，要不然太胖了</div><br/><div><signature id=\"initial\"><table cellpadding=\"0\" cellspacing=\"0\"><tbody><tr><td style=\"vertical-align:top;width:1px\"></td><td><div>这是一个签名测试</div></td></tr><tr><td colspan=\"2\"><div style=\"font-size:0.9em;border-top:1px dashed gray;min-width:250px;max-width:400px;margin-top:10px;padding-top:4px\"><div><a style=\"margin-right:8px\" href=\"mailto:test@abc.com\">test@abc.com</a></div><div></div><div><a href=\"https://maps.google.com/?q=åäº¬åé¾è§ä¸å¤§è¡3\">北京回龙观东大街3</a></div></div></td></tr></tbody></table></signature></div>",
		"cc": [],
		"date": 1536217708,
		"draft": true,
		"events": [],
		"file_ids": [],
		"files": [],
		"folder": {
			"__cls": "Folder",
			"aid": "9b3a80a0", // account_id
			"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa", // 文件夹id
			"localStatus": { // 为了显示本地文件夹同步的状况（由mailsync计算得出）, 详细请参考<a href="SyncbackDraftTask.md" target="_blank">【SyncbackDraftTask.md】</a>
				"bodiesPresent": 5,
				"bodiesWanted": 0,
				"busy": false,
				"highestmodseq": 0,
				"lastCleanup": 1536216770,
				"lastDeep": 1536217394,
				"lastShallow": 1536217522,
				"syncedMinUID": 1,
				"uidnext": 7,
				"uidvalidity": 114,
				"uidvalidityResetCount": 0
			},
			"path": "Drafts", // 路径
			"role": "drafts",
			"v": 1024 // 版本号, 每次调用+1
		},
		"from": [{
			"__cls": "Contact",
			"aid": "9b3a80a0",
			"email": "quanzhansheng@msn.com",
			"id": "local-9b3a80a0-me",
			"name": "haha pipi",
			"thirdPartyData": {}
		}],
		"gMsgId": "",
		"hMsgId": "1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com", // (客户端生成)同headerMessageId
		"id": "fwHwGQpYorWyZpF1hs5RmbbMynVmXKXZZvRVTuThq", // 草稿id
		"labels": [],
		"metadata": [{
			"__cls": "PluginMetadata",
			"pluginId": "link-tracking",
			"v": 1,
			"value": {
				"tracked": true
			}
		}, {
			"__cls": "PluginMetadata",
			"pluginId": "open-tracking",
			"v": 1,
			"value": {
				"open_count": 0,
				"open_data": []
			}
		}, {
			"__cls": "PluginMetadata",
			"pluginId": "send-later",
			"v": 2,
			"value": {
				"expiration": null
			}
		}],
		"object": "draft",
		"pristine": false,
		"remoteFolder": { // 同上面的folder
			"__cls": "Folder",
			"aid": "9b3a80a0",
			"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa",
			"localStatus": {
				"bodiesPresent": 5,
				"bodiesWanted": 0,
				"busy": false,
				"highestmodseq": 0,
				"lastCleanup": 1536216770,
				"lastDeep": 1536217394,
				"lastShallow": 1536217522,
				"syncedMinUID": 1,
				"uidnext": 7,
				"uidvalidity": 114,
				"uidvalidityResetCount": 0
			},
			"path": "Drafts",
			"role": "drafts",
			"v": 1024
		},
		"remoteUID": 0,
		"replyTo": [],
		"starred": false,
		"subject": "下午好，晚上吃点啥", // 标题
		"threadId": "",
		"to": [{
			"__cls": "Contact",
			"aid": "9b3a80a0",
			"email": "qzs0390@sina.com",
			"id": "qzs0390@sina.com",
			"name": "qzs0390@sina.com",
			"refs": 4,
			"thirdPartyData": {}
		}],
		"unread": false,
		"v": 11
	}],
	"type": "persist"
}
```

- JSON-2
```json
{
	"modelClass": "Task",
	"modelJSONs": [{
		"__cls": "SyncbackDraftTask",
		"aid": "9b3a80a0",
		"draft": {
			"__cls": "Message",
			"aid": "9b3a80a0",
			"bcc": [],
			"body": "<div>吃点低热量的，要不然太胖了</div><br/><div><signature id=\"initial\"><table cellpadding=\"0\" cellspacing=\"0\"><tbody><tr><td style=\"vertical-align:top;width:1px\"></td><td><div>这是一个签名测试</div></td></tr><tr><td colspan=\"2\"><div style=\"font-size:0.9em;border-top:1px dashed gray;min-width:250px;max-width:400px;margin-top:10px;padding-top:4px\"><div><a style=\"margin-right:8px\" href=\"mailto:test@abc.com\">test@abc.com</a></div><div></div><div><a href=\"https://maps.google.com/?q=åäº¬åé¾è§ä¸å¤§è¡3\">北京回龙观东大街3</a></div></div></td></tr></tbody></table></signature></div>",
			"cc": [],
			"date": 1536217701,
			"draft": true,
			"events": [],
			"file_ids": [],
			"files": [],
			"folder": {
				"__cls": "Folder",
				"aid": "9b3a80a0",
				"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa",
				"localStatus": {
					"bodiesPresent": 5,
					"bodiesWanted": 0,
					"busy": false,
					"highestmodseq": 0,
					"lastCleanup": 1536216770,
					"lastDeep": 1536217394,
					"lastShallow": 1536217522,
					"syncedMinUID": 1,
					"uidnext": 7,
					"uidvalidity": 114,
					"uidvalidityResetCount": 0
				},
				"path": "Drafts",
				"role": "drafts"
			},
			"from": [{
				"__cls": "Contact",
				"aid": "9b3a80a0",
				"email": "quanzhansheng@msn.com",
				"id": "local-9b3a80a0-me",
				"name": "haha pipi",
				"thirdPartyData": {}
			}],
			"hMsgId": "1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com",
			"id": "fwHwGQpYorWyZpF1hs5RmbbMynVmXKXZZvRVTuThq",  // 草稿id
			"metadata": [{
				"__cls": "PluginMetadata",
				"pluginId": "link-tracking",
				"v": 1,
				"value": {
					"tracked": true
				}
			}, {
				"__cls": "PluginMetadata",
				"pluginId": "open-tracking",
				"v": 1,
				"value": {
					"open_count": 0,
					"open_data": []
				}
			}, {
				"__cls": "PluginMetadata",
				"pluginId": "send-later",
				"v": 2,
				"value": {
					"expiration": null
				}
			}],
			"object": "draft",
			"pristine": false,
			"replyTo": [],
			"starred": false,
			"subject": "下午好，晚上吃点啥",
			"threadId": "",
			"to": [{
				"__cls": "Contact",
				"aid": "9b3a80a0",
				"email": "qzs0390@sina.com",
				"id": "qzs0390@sina.com",
				"name": "qzs0390@sina.com",
				"refs": 4,
				"thirdPartyData": {}
			}],
			"unread": false,
			"v": 7
		},
		"headerMessageId": "1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com",
		"id": "local-9a89cff2-0458",
		"status": "local",
		"v": 1
	}],
	"type": "persist"
}
```

- JSON-3
```json
{
	"modelClass": "Task",
	"modelJSONs": [{
		"__cls": "SyncbackDraftTask",
		"aid": "9b3a80a0",
		"draft": {
			"__cls": "Message",
			"_sa": 0,
			"_suc": 0,
			"aid": "9b3a80a0",
			"bcc": [],
			"body": "<div>吃点低热量的，要不然太胖了</div><br/><div><signature id=\"initial\"><table cellpadding=\"0\" cellspacing=\"0\"><tbody><tr><td style=\"vertical-align:top;width:1px\"></td><td><div>这是一个签名测试</div></td></tr><tr><td colspan=\"2\"><div style=\"font-size:0.9em;border-top:1px dashed gray;min-width:250px;max-width:400px;margin-top:10px;padding-top:4px\"><div><a style=\"margin-right:8px\" href=\"mailto:test@abc.com\">test@abc.com</a></div><div></div><div><a href=\"https://maps.google.com/?q=åäº¬åé¾è§ä¸å¤§è¡3\">北京回龙观东大街3</a></div></div></td></tr></tbody></table></signature></div>",
			"cc": [],
			"date": 1536217708,
			"draft": true,
			"events": [],
			"file_ids": [],
			"files": [],
			"folder": {
				"__cls": "Folder",
				"aid": "9b3a80a0",
				"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa",
				"localStatus": {
					"bodiesPresent": 5,
					"bodiesWanted": 0,
					"busy": false,
					"highestmodseq": 0,
					"lastCleanup": 1536216770,
					"lastDeep": 1536217394,
					"lastShallow": 1536217522,
					"syncedMinUID": 1,
					"uidnext": 7,
					"uidvalidity": 114,
					"uidvalidityResetCount": 0
				},
				"path": "Drafts",
				"role": "drafts",
				"v": 1024
			},
			"from": [{
				"__cls": "Contact",
				"aid": "9b3a80a0",
				"email": "quanzhansheng@msn.com",
				"id": "local-9b3a80a0-me",
				"name": "haha pipi",
				"thirdPartyData": {}
			}],
			"gMsgId": "",
			"hMsgId": "1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com",
			"id": "fwHwGQpYorWyZpF1hs5RmbbMynVmXKXZZvRVTuThq",  // 草稿id
			"labels": [],
			"metadata": [{
				"__cls": "PluginMetadata",
				"pluginId": "link-tracking",
				"v": 1,
				"value": {
					"tracked": true
				}
			}, {
				"__cls": "PluginMetadata",
				"pluginId": "open-tracking",
				"v": 1,
				"value": {
					"open_count": 0,
					"open_data": []
				}
			}, {
				"__cls": "PluginMetadata",
				"pluginId": "send-later",
				"v": 2,
				"value": {
					"expiration": null
				}
			}],
			"object": "draft",
			"pristine": false,
			"remoteFolder": {
				"__cls": "Folder",
				"aid": "9b3a80a0",
				"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa",
				"localStatus": {
					"bodiesPresent": 5,
					"bodiesWanted": 0,
					"busy": false,
					"highestmodseq": 0,
					"lastCleanup": 1536216770,
					"lastDeep": 1536217394,
					"lastShallow": 1536217522,
					"syncedMinUID": 1,
					"uidnext": 7,
					"uidvalidity": 114,
					"uidvalidityResetCount": 0
				},
				"path": "Drafts",
				"role": "drafts",
				"v": 1024
			},
			"remoteUID": 0,
			"replyTo": [],
			"starred": false,
			"subject": "下午好，晚上吃点啥",
			"threadId": "",
			"to": [{
				"__cls": "Contact",
				"aid": "9b3a80a0",
				"email": "qzs0390@sina.com",
				"id": "qzs0390@sina.com",
				"name": "qzs0390@sina.com",
				"refs": 4,
				"thirdPartyData": {}
			}],
			"unread": false,
			"v": 7
		},
		"headerMessageId": "1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com",
		"id": "local-9a89cff2-0458",
		"status": "remote",
		"v": 2
	}],
	"type": "persist"
}
```

- JSON-4
```json
{
	"modelClass": "Message",
	"modelJSONs": [{
		"__cls": "Message",
		"_sa": 1536217720, // 发送时间
		"_suc": 0,
		"aid": "9b3a80a0", // account_id
		"bcc": [],
		"cc": [],
		"date": 1536217708,
		"draft": false,
		"files": [],
		"folder": { // 文件路径
			"__cls": "Folder",
			"aid": "9b3a80a0",
			"id": "sSHyCptXqBTMSDpnsh8yxW5o1FH6t2y3CbGHcdxuq", // 文件夹的id，sqlite中ThreadCategory的value就是这个值。
			"path": "Sent", // 从Draft移动到Sent
			"role": "sent",
			"v": 1026
		},
		"from": [{
			"email": "quanzhansheng@msn.com",
			"name": "haha pipi"
		}],
		"gMsgId": "0",
		"hMsgId": "1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com", // 同headerMessageId
		"id": "CTL1UmXPu31ndj7L4sZVUHhd84FjP6tTT6SnUTTbv", // 邮件id
		"labels": [],
		"metadata": [{ // 插件
			"pluginId": "link-tracking",
			"v": 1,
			"value": {
				"links": [{
					"click_count": 0,
					"click_data": [],
					"redirect_url": "https://link.getmailspring.com/link/1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com/0?redirect=mailto:test@abc.com",
					"url": "mailto:test@abc.com"
				}, {
					"click_count": 0,
					"click_data": [],
					"redirect_url": "https://link.getmailspring.com/link/1536217630.local-7ce89f99-f0db-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com/1?redirect=https://maps.google.com/?q=%E5%8C%97%E4%BA%AC%E5%9B%9E%E9%BE%99%E8%A7%82%E4%B8%9C%E5%A4%A7%E8%A1%973",
					"url": "https://maps.google.com/?q=åäº¬åé¾è§ä¸å¤§è¡3"
				}],
				"tracked": true
			}
		}, {
			"pluginId": "open-tracking",
			"v": 1,
			"value": {
				"open_count": 0,
				"open_data": []
			}
		}, {
			"pluginId": "send-later",
			"v": 1,
			"value": {
				"expiration": null
			}
		}],
		"remoteFolder": { // 同上面的folder信息
			"__cls": "Folder",
			"aid": "9b3a80a0",
			"id": "sSHyCptXqBTMSDpnsh8yxW5o1FH6t2y3CbGHcdxuq",
			"path": "Sent",
			"role": "sent",
			"v": 1026
		},
		"remoteUID": 18,
		"replyTo": [],
		"rtMsgId": null,
		"snippet": "吃点低热量的，要不然太胖了 这是一个签名测试 test@abc.com (mailto:test@abc.com) 北京回龙观东大街3 (https://maps.google.com/?q=åäº¬åé¾è§ä¸å¤§è¡3)", // 邮件正文片段
		"starred": false,
		"subject": "下午好，晚上吃点啥",
		"threadId": "t:CTL1UmXPu31ndj7L4sZVUHhd84FjP6tTT6SnUTTbv", // Thread Id
		"to": [{
			"email": "qzs0390@sina.com",
			"name": "qzs0390@sina.com"
		}],
		"unread": false,
		"v": 5
	}],
	"type": "persist"
}
```
