# ԭ������ȱʧʱ�ĸ߼��������

ֻ�������� Lovart����ǰԭ�����߲��ɼ����������������ʱʹ�á����� app-server ����ÿ�����ɵıؾ��Ž�������Ҫ�ƹ���������ȡƾ�ݻ��˽������ԭ����ӡ������

�ȶ�ȡ���� `codex --version` �� `codex app-server --help`����Ҫʱʹ�øð汾֧�ֵ� `generate-json-schema --experimental --out <��ʱĿ¼>` ��ȡЭ�顣��ʵ�ʰ�װ�汾Ϊ׼����ֱ�Ӹ��������ĵ��������ֶΡ�

## �汾����

���� Windows��codex-cli 0.153.2 ʹ�ö��� `codex app-server --stdio` ��Ϲ��߼��ء�����Ϊ�ð汾�� JSONL ˳��ʾ�������ǿ�汾�̶�Э�顣���� initialize ���ȵȴ���Ӧ���ٷ��� initialized ��״̬��ѯ��

```jsonl
{"id":0,"method":"initialize","params":{"clientInfo":{"name":"lovart_mcp_diagnostics","version":"1.0"},"capabilities":{"experimentalApi":true}}}
{"method":"initialized","params":{}}
{"id":1,"method":"mcpServerStatus/list","params":{"detail":"toolsAndAuthOnly"}}
```

�ӷ��ؽ��ɸѡ `name=lovart`�����ʵ�����Ӵ���`authStatus` ��ʾ OAuth ֻ������Ȩ״̬��¼����֤�������Կ�ʹ�á�����ʷ�����У�״̬�б����� OAuth ��ǣ�������ͬʱ���� `invalid_grant`��ֻ�к��߲���������Ȩ����ȷ���ݡ�

## �����߽�

- ����ʷ�汾�� `ListMcpServerStatusParams` û�� `serverName` �ֶΣ������汾����֧�֡��ȼ�鱾�� schema��
- `config/mcpServer/reload` �ڸð汾��ʹ�� `params=null`������ǰ�˶�����֧����������ٶ���ǰ����Ựһ����Ӱ�졣
- ���� app-server �� reload �ɹ�����֤�����������̻�ǰ�Ի��Ĺ���Ŀ¼�Ѿ�ˢ�¡�
- `mcpServer/tool/call` �ڸð汾Ҫ�� `server`��`threadId`��`tool` �� `arguments`���������ͨ������ִ��������ȷ�б�Ҫ��ֻ����ʵ�ʷ����ҵ�ǰ������Ȩ�Ĺ��ߡ�
- ĳЩ����û�� daemon ��������������� `daemon restart` ����ͨ�ûָ�������
- �����ܲ��ṩ����ר���Žӽű���ԭ�����߻ָ���ֱ��ʹ��ԭ���ӿڣ�����ʱ��ڶ���������·��

ʵ����֤ʧЧʱ�� SKILL.md �ָ� OAuth����ɺ����·��ֹ��ߣ����ڵ�ǰ����ȷ������ʱʹ��ֻ���ӿ���֤����Ҫ��������ҳ��¼������Ϊ��ϵĹ̶�ǰ�ò��衣

�ٷ����ݣ�[Codex App Server](https://learn.chatgpt.com/docs/app-server)��
