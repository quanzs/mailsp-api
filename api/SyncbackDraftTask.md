# 保存草稿
## native端业务逻辑猜想
- 1. 把接收到的数据保存到sqlite的Task表中，status为local，同时返回给客户端JSON-1和JSON-2
- 2. 开始与邮件服务器同步，成功后status变为remote
- 3. 返回JSON-3
- 4. Task表中，status变为complete

## Post Data
```json
{
	"type": "queue-task", // 类型，供native区分业务
	"task": {
		"id": "local-f65a1fe7-5608", // (客户端生成)任务id。规则：`local-${s4()}${s4()}-${s4()}`
		"aid": "9b3a80a0", // account_id
		"status": "local", // 状态（保存到sqlite的task表中）
		"headerMessageId": "1536029080.local-88c66397-74e7-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com", // (客户端生成)headerMessageId，Thread的原始邮件的id。规则：`${uniqueId}-v${AppEnv.getVersion()}@getmailspring.com`
		"draft": { // 草稿信息
			"aid": "9b3a80a0",
			"metadata": [],
			"to": [],
			"cc": [],
			"bcc": [],
			"from": [{
				"id": "local-9b3a80a0-me", // (客户端生成) 规则：`local-${account_id}-me`
				"aid": "9b3a80a0",
				"name": "haha pipi",
				"email": "quanzhansheng@msn.com",
				"thirdPartyData": {},
				"__cls": "Contact"
			}],
			"replyTo": [],
			"date": 1536029080.768, // 生成的时间
			"body": "<br/><br/><signature id=\"initial\"><div><div>Sent from <a href=\"https://getmailspring.com/\">Mailspring</a>, the best free email app for work</div></div></signature>", // 邮件内容(包含签名)
			"files": [{ // 附件
				"id": "local-8afaab0a-8d46", // (客户端生成)附件id。规则：`local-${s4()}${s4()}-${s4()}`
				"filename": "DestroyDraftTask.md", // 附件的文件名
				"size": 247, // 附件文件大小
				"contentType": null, // 固定为null
				"messageId": null, // 固定为null
				"contentId": null, // null or （客户端生成）规则： `mcid-${s4()}${s4()}-${s4()}`
				"__cls": "File"
			}],
			"unread": false,
			"events": [],
			"starred": false,
			"hMsgId": "1536029080.local-88c66397-74e7-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com", // 同headerMessageId
			"subject": "", // 标题
			"draft": true, // 是否为草稿 flag
			"pristine": true, // By default, new drafts are considered `pristine`. If the user leaves the composer without making any changes, the draft is discarded. If your extension populates the draft in a way that makes it "populated" in a valuable way, you should set `draft.pristine = false` so the draft saves, even if no further changes are made.
			"v": 0,
			"__cls": "Message",
			"file_ids": ["local-8afaab0a-8d46"], // 附件的id数组
			"object": "draft"
		},
		"__cls": "SyncbackDraftTask"
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
		"aid": "9b3a80a0", // account_id
		"bcc": [],
		"body": "<br/><br/><signature id=\"initial\"><div><div>Sent from <a href=\"https://getmailspring.com/\">Mailspring</a>, the best free email app for work</div></div></signature>",
		"cc": [],
		"date": 1536029080,
		"draft": true,
		"events": [],
		"file_ids": ["local-8afaab0a-8d46"], // 附件id
		"files": [{ // 附件
			"__cls": "File",
			"contentId": null,
			"contentType": null,
			"filename": "DestroyDraftTask.md",
			"id": "local-8afaab0a-8d46",
			"messageId": null,
			"size": 247
		}],
		"folder": { // 邮件所在文件夹
			"__cls": "Folder",
			"aid": "9b3a80a0", // account_id 
			"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa", // folder的id信息, 即sqlite中Folder表的id
			"localStatus": { 
				// 为了显示本地文件夹同步的状况（由mailsync计算得出）
				// uidnext:用来调节权重weight的, weight = uidnext / 10000
				// 最终用来计算进度。 进度的包括两个部分： 
				// 第一个部分：scanProgress = 1.0 - (syncedMinUID - 1) / uidnext
				// 第二部分：bodyProgress = bodiesPresent / bodiesWanted
				// 总进度 = scanProgress * weight * 0.4 + bodyProgress * weight * 0.6
				"bodiesPresent": 8,
				"bodiesWanted": 2,
				"busy": false, // （猜测）native是否正在进行其他处理 
				"highestmodseq": 0, // （未使用）意义不明
				"lastCleanup": 1536027626,
				"lastDeep": 1536028891, //（猜测）最后深同步时间
				"lastShallow": 1536029017, //（猜测）最后浅同步时间
				"syncedMinUID": 1,
				"uidnext": 7, 
				"uidvalidity": 114, // （未使用）意义不明
				"uidvalidityResetCount": 0 // （未使用）意义不明
			},
			"path": "Drafts", // 文件夹路径
			"role": "drafts", // 首字母小写的文件夹路径
			"v": 535 // 版本号, 每次调用+1
		},
		"from": [{ // 发件人
			"__cls": "Contact",
			"aid": "9b3a80a0", // account_id 
			"email": "quanzhansheng@msn.com",
			"id": "local-9b3a80a0-me",
			"name": "haha pipi",
			"thirdPartyData": {}
		}],
		"gMsgId": "", // （未使用）g看起来像是global的缩写
		"hMsgId": "1536029080.local-88c66397-74e7-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com", // (客户端生成)同headerMessageId
		"id": "RZXwX2z22CWc1ckCekCV7pQQ3bMwCk6sX9BeQS3bJ", // 邮件id
		"labels": [], // 标签 gmail有标签, hotmail没有
		"metadata": [],
		"object": "draft",
		"pristine": true, // 意义同postdata中的pristine
		"remoteFolder": { // 同上面的folder信息
			"__cls": "Folder",
			"aid": "9b3a80a0",
			"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa",
			"localStatus": {
				"bodiesPresent": 8,
				"bodiesWanted": 2,
				"busy": false,
				"highestmodseq": 0,
				"lastCleanup": 1536027626,
				"lastDeep": 1536028891,
				"lastShallow": 1536029017,
				"syncedMinUID": 1,
				"uidnext": 7,
				"uidvalidity": 114,
				"uidvalidityResetCount": 0
			},
			"path": "Drafts",
			"role": "drafts",
			"v": 535
		},
		"remoteUID": 0,
		"replyTo": [],
		"starred": false,
		"subject": "",
		"threadId": "",
		"to": [],
		"unread": false,
		"v": 1
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
			"body": "<br/><br/><signature id=\"initial\"><div><div>Sent from <a href=\"https://getmailspring.com/\">Mailspring</a>, the best free email app for work</div></div></signature>",
			"cc": [],
			"date": 1536029080.768,
			"draft": true,
			"events": [],
			"file_ids": ["local-8afaab0a-8d46"],
			"files": [{
				"id": "local-8afaab0a-8d46",
				"filename": "DestroyDraftTask.md",
				"size": 247,
				"contentType": null,
				"messageId": null,
				"contentId": null,
				"__cls": "File"
			}],
			"from": [{
				"__cls": "Contact",
				"aid": "9b3a80a0",
				"email": "quanzhansheng@msn.com",
				"id": "local-9b3a80a0-me",
				"name": "haha pipi",
				"thirdPartyData": {}
			}],
			"hMsgId": "1536029080.local-88c66397-74e7-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com",
			"metadata": [],
			"object": "draft",
			"pristine": true,
			"replyTo": [],
			"starred": false,
			"subject": "",
			"to": [],
			"unread": false,
			"v": 0
		},
		"headerMessageId": "1536029080.local-88c66397-74e7-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com",
		"id": "local-f65a1fe7-5608",
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
			"body": "<br/><br/><signature id=\"initial\"><div><div>Sent from <a href=\"https://getmailspring.com/\">Mailspring</a>, the best free email app for work</div></div></signature>",
			"cc": [],
			"date": 1536029080,
			"draft": true,
			"events": [],
			"file_ids": ["local-8afaab0a-8d46"],
			"files": [{
				"id": "local-8afaab0a-8d46",
				"filename": "DestroyDraftTask.md",
				"size": 247,
				"contentType": null,
				"messageId": null,
				"contentId": null,
				"__cls": "File"
			}],
			"folder": {
				"__cls": "Folder",
				"aid": "9b3a80a0",
				"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa",
				"localStatus": {
					"bodiesPresent": 8,
					"bodiesWanted": 2,
					"busy": false,
					"highestmodseq": 0,
					"lastCleanup": 1536027626,
					"lastDeep": 1536028891,
					"lastShallow": 1536029017,
					"syncedMinUID": 1,
					"uidnext": 7,
					"uidvalidity": 114,
					"uidvalidityResetCount": 0
				},
				"path": "Drafts",
				"role": "drafts",
				"v": 535
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
			"hMsgId": "1536029080.local-88c66397-74e7-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com",
			"id": "RZXwX2z22CWc1ckCekCV7pQQ3bMwCk6sX9BeQS3bJ",
			"labels": [],
			"metadata": [],
			"object": "draft",
			"pristine": true,
			"remoteFolder": {
				"__cls": "Folder",
				"aid": "9b3a80a0",
				"id": "FC2XUa2JdUnPuMQgvegDeViuoW4Ug43GaGMYNQvQa",
				"localStatus": {
					"bodiesPresent": 8,
					"bodiesWanted": 2,
					"busy": false,
					"highestmodseq": 0,
					"lastCleanup": 1536027626,
					"lastDeep": 1536028891,
					"lastShallow": 1536029017,
					"syncedMinUID": 1,
					"uidnext": 7,
					"uidvalidity": 114,
					"uidvalidityResetCount": 0
				},
				"path": "Drafts",
				"role": "drafts",
				"v": 535
			},
			"remoteUID": 0,
			"replyTo": [],
			"starred": false,
			"subject": "",
			"threadId": "",
			"to": [],
			"unread": false,
			"v": 0
		},
		"headerMessageId": "1536029080.local-88c66397-74e7-v1.4.2-COMMIT_INSERTED_DURING_PACKAGING@getmailspring.com",
		"id": "local-f65a1fe7-5608",
		"status": "remote",
		"v": 2
	}],
	"type": "persist"
}
```