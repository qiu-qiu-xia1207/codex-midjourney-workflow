---
name: lovart-mcp-workflow
description: Use Codex with Lovart MCP to generate and iteratively improve Midjourney images, including reference images and sref, prompt and parameter planning, candidate evaluation, refinement, result delivery, and connection or generation troubleshooting.
---

# Midjourney ��ͼ���Ż���������Codex + Lovart MCP

ͨ��Lovart MCP����Midjourney����ɲο�ͼ׼��������̽������ѡ�Ƚϡ��������Ż�����������������ڲ廭����ɫ������ͼ�������û�ָ����ͼ�����񣻲�Ԥ��ĳ�ֻ��硢ĳ����Ŀ��̶��������������ﲻ��ֱ��Midjourney�ٷ�API����ȷ�༭�����û���Ȩ��ʹ���ʺϵ�����ģ�ͣ���������¼��Դ��

## ����ȷ��������

- ��ȡ��ǰ��Ŀ���е������ġ��ο�ͼ�������¼����manifest����ȷ����ĿID��Ŀ�ꡢѡ��ģ�͡�����Ԥ�㡢����������û��Ѿ�������ѡ��
- �û��Ѿ���Ȩ�Ĳ���ֱ�Ӽ�������Ҫ�ѶԻ��жϵ��������ύ���ɵ���Ȩ��ȱ��Ԥ��ȹؼ��߽�ʱ����ȷ��С���鷶Χ��������������չ��
- �û�Ҫ��ȴ���ֻ�ܽᾭ���ֻ��д��ʾ��ʱ�����������ɡ�ֻ�е�ǰ�������������֤��ʵ������ʱ��������Ҫ���˻���顣
- �����ӿ��á����񱻽��ա�������ɡ����������û����÷ֱ��¼��ֻ���ӿڳɹ�����֤��ĳ��ģ�����ɹ����ѻָ���

## �����������Ż�·��

ʵ��ִ��ʱ��ȡ[Midjourney�ύ���������Ż�](references/midjourney.md)��ֻ���뱾��Ŀ����صĽ׶Σ�

1. **������н��롣** ���ȷ���ԭ��Lovart���ߣ����߿���ʱ��get_credits��ֻ���ӿ�ȷ�ϱ����˻����ʡ���ҳ��¼��computer-use����Ĭ����ͼ��ڡ�
2. **�˶�ģ����ο���** ��list_models����Midjourney·�ɣ���describe_model��ȡ���������model��Lovart·�ɣ�params.model��Midjourney�汾��ͼ������������sref��������ֿ���¼��sref����֤����������
3. **׼�����ύ��** �����û�ָ��project_id���ⲿ�����زο��Ⱦ�upload_asset���Lovart URL�����йܵ�ͬһ�ļ��ɸ��á������������������ȵǼǣ����غ���������task_ids��ʧ��Ҳ����Ԥ�㡣
4. **����ȫ�������** pending��poll_after_seconds�ȴ�����Ҫ�ڲ���ѯʱ��get_task_status����̬��ѭ����ѯ�����ɻغϰ����߹�����һ��get_taskչʾ����ȫ������ID����Ҫ��ͼ��include_images=true��
5. **�����Ƚϡ�** ʵ�ʲ鿴��ѡ�����û�Ŀ���鹹ͼ�����塢ϸ�ڡ����缰ʹ�óߴ硣��Ҫɸѡʱ�����߹���ʹ��rank_candidates_with_jev������ʵ�ʻ��棻�������ܴ����û����á�����ȫ����ѡ��
6. **�������Ż���** ���巽��ƫ������ȨԤ���ڼ���Midjourney̽����ѡ�������ľֲ��޸�ʹ������Ȩ�༭���ߡ�ÿ��д��ı����뱣���������༭����Դͼ���ա�������չ����ͼ���Ŵ�͵����ֱ���飬������ԭ����

����ʹ�õ�ǰschema��������ƣ�prompt��aspect_ratio�����ж����ֶβ���params�ظ�����ʷ�ɹ�����ֻ����Ϊ���ӣ�����ͨ�����ֵ��δ������ԵĲ�����Ϊ��������δ֤ʵ����n��ʵ�ʲ������ֿ�ͳ�ƣ����˻�concurrency_limit���Ų�������Ӳ���롣

## �����쳣ʱ��֤�ݴ���

| �۲��� | ���� |
| --- | --- |
| ԭ��ֻ���ӿڳɹ� | ������Ȩ���񣬲��ظ���¼ |
| ����Ŀ¼û��Lovart | �˲��������ú���������״̬������Ҫ����ҳ��¼ |
| CLI��ʾAuth Unknown��OAuth��� | ����״̬������Unknown������δ��Ȩ��OAuth��ǲ�����������Ч |
| ʵ�����ӷ���invalid_grant��ˢ�����Ʊ��ܾ� | ʹ��MCP OAuth�ָ�������ֻ֤���ӿ� |
| ��֤��Ч������δ���� | �������֧�ֵ�״̬�����ط�ʽ����Ҫʱ��ȡ[�߼��������](references/app-server.md) |
| �������ύ��failed��API_CALL_ERROR | ��¼Ϊ����ʧ�ܣ��ô����������ص�¼���� |

���ü��ֻ���������������״̬������������URL������JSON���ڲ�����������ӡ��֤ͷ�����ƻ�����˽���ֶΣ�

```powershell
codex mcp list
$lovartConfig = codex mcp get lovart --json | ConvertFrom-Json
[pscustomobject]@{
  name = $lovartConfig.name
  enabled = $lovartConfig.enabled
  transport = $lovartConfig.transport.type
  url = $lovartConfig.transport.url
}
```

ֻ������ȷʵȱʧ�����ù����ѻ���Ȩʱ����������֪���룺

```powershell
codex mcp add lovart --url https://lgw.lovart.ai/v1/openapi/mcp/widgets --oauth-client-id codex-lovart
```

ֻ����ȷ��֤ʧ�ܲŻָ�OAuth��

```powershell
codex mcp login lovart
```

����ʵ����Ч���ã���Ĭ��logout��remove���ظ�add��OAuth�ɹ������·���ԭ�����߲�ֻ����֤������Ԥ��Ҫ����������Ӧ�á�����������ڱ�Ҫ����Ȩ����ҳ��¼��MCP��Ȩ�ֱ�����������cookies�����ƻ���Ȩ��Ļص�URL����¼��ƾ���ɱ���Codex���������漼�ַܷ���

## ʧ��ֹͣ�뽻��

- ��ʱ����Ӧ��ʧʱ�Ⱥ˶�ԭ����״̬δ֪������ʧ�ܣ����ظ��ύ��completed��failed��moderated����ֻ̬���浵������get_taskչʾ���ò���Ϊѭ����ѯ��
- API_CALL_ERRORȱ������ʱ����ԭ��δ֪��������ȨԤ�����о������ݣ���������һ���ܿ����ԣ�ͬ���ظ���ֹͣ�Ʒѳ��ԡ�������ͨ�ô�����Ϲ�����HD��iw����ʾ�ʳ��ȡ�
- �û��ܾ�����ʱ�����Ҳ��Զ���չ���û�ָ������Midjourney�ȴ�ʱ����ģ�ͣ�Ҳ�����ź�̨���ԡ��û�����������������ʷ������Ȩ��
- �����������󡢲ο��ֹ���ģ��·�ɡ�ʵ������������������������û�����״̬�������㡣��¼ģ�ͣ�������Դ��δʵ�ֵı༭��͸������������滻��д������ɡ�

��Ҫ������Щ�жϵ���������ʱ����ȡ[���������ɹ��Ͼ���](references/session-lessons.md)�����ܿ��Լ����ظ����������ã����ܱ�֤�ⲿ����ʼ�տ��û�OAuth�������ڡ�

�ٷ����ݣ�[Codex MCP](https://learn.chatgpt.com/docs/extend/mcp)��[App Server](https://learn.chatgpt.com/docs/app-server)��ִ��ʱ�Ե�ǰ������Lovart����schemaΪ׼��
