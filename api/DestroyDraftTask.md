# 删除草稿
## native端业务逻辑猜想
- 更新sqlite的Message表中的该条数据
  - id: deleted-40位随机字符串
  - headerId: deleted-deleted-40位随机字符串（同id的随机字符串相同）
  - subject: Deleting... (固定)
- 在Task表中新建一条数据, status为local

## Post Data
```json
{
	"type": "queue-task",
	"task": {
		"id": "local-7a134c15-b449", // (客户端生成)任务id。规则：`local-${s4()}${s4()}-${s4()}`
		"aid": "9b3a80a0", // account_id
		"status": "local",
		"messageIds": ["AStjsGNw1Y29F2UMYLeZ1hvXRAM6XgpzbUFoGqVW7"], // 对应数据库中Message表中的id
		"__cls": "DestroyDraftTask"
	}
}
```

## Response data
没有注释的字段的含义可以参考<a href="SyncbackDraftTask.md" target="_blank">【SyncbackDraftTask.md】</a>
```json
{
	"modelClass": "Message",
	"modelJSONs": [{
		"__cls": "Message",
		"_sa": 0,
		"_suc": 0,
		"aid": "9b3a80a0", // account_id
		"bcc": [],
		"body": "<div>1234</div><br/><div><signature id=\"initial\"><table cellpadding=\"0\" cellspacing=\"0\"><tbody><tr><td style=\"vertical-align:top;width:1px\"></td><td><div>这是一个签名测试</div></td></tr><tr><td colspan=\"2\"><div style=\"font-size:0.9em;border-top:1px dashed gray;min-width:250px;max-width:400px;margin-top:10px;padding-top:4px\"><div><a style=\"margin-right:8px\" href=\"mailto:test@abc.com\">test@abc.com</a></div><div></div><div><a href=\"https://maps.google.com/?q=åäº¬åé¾è§ä¸å¤§è¡3\">北京回龙观东大街3</a></div></div></td></tr></tbody></table></signature></div>",
		"cc": [],
		"date": 1536111566,
		"draft": true,
		"events": [],
		"file_ids": [],
		"files": [],
		"folder": {
			"__cls": "Folder",
			"aid": "9b3a80a0",
			"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa",
			"localStatus": { // 为了显示本地文件夹同步的状况（由mailsync计算得出）, 详细请参考<a href="SyncbackDraftTask.md" target="_blank">【SyncbackDraftTask.md】</a>
				"bodiesPresent": 11,
				"bodiesWanted": 0,
				"busy": false,
				"highestmodseq": 0,
				"lastCleanup": 1536111022,
				"lastDeep": 1536111147,
				"lastShallow": 1536111525,
				"syncedMinUID": 1,
				"uidnext": 7,
				"uidvalidity": 114,
				"uidvalidityResetCount": 0
			},
			"path": "Drafts",
			"role": "drafts",
			"v": 655
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
		"hMsgId": "1536111465.local-960982d1-d135-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com",
		"id": "AStjsGNw1Y29F2UMYLeZ1hvXRAM6XgpzbUFoGqVW7",
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
		}],
		"object": "draft",
		"pristine": false,
		"remoteFolder": {
			"__cls": "Folder",
			"aid": "9b3a80a0",
			"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa",
			"localStatus": {
				"bodiesPresent": 11,
				"bodiesWanted": 0,
				"busy": false,
				"highestmodseq": 0,
				"lastCleanup": 1536111022,
				"lastDeep": 1536111147,
				"lastShallow": 1536111525,
				"syncedMinUID": 1,
				"uidnext": 7,
				"uidvalidity": 114,
				"uidvalidityResetCount": 0
			},
			"path": "Drafts",
			"role": "drafts",
			"v": 655
		},
		"remoteUID": 0,
		"replyTo": [],
		"starred": false,
		"subject": "1测试测试哦",
		"threadId": "",
		"to": [{
			"__cls": "Contact",
			"email": "g99d@test.com",
			"name": "g99d@test.com",
			"thirdPartyData": {}
		}],
		"unread": false,
		"v": 9
	}],
	"type": "unpersist"
}
```