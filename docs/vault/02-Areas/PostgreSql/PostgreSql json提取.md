---
date created: 2025-08-28 11:32
---

示例：

```json
{"Document":{"CreateTime":"2025-08-26 17:30:00"},"v_emr_doc_住院病历信息":{"患者姓名":"吴惠芳","性别代码":"2","年龄":"65","主诉":"头部外伤后疼痛、头晕2天"}}
```

提取主诉
`(content)::json #>> '{v_emr_doc_住院病历信息,主诉}'`

复杂统计示例
数据格式：
```json  
  [{"itemKey":"SCALE0000014","fixedKey":true,"title":"病案号","dataName":"患者信息","type":"NOT_EDITABLE","typeName":"内置控件","placeholder":"请输入","suffix":""},{"type":"RADIO_BUTTON","typeName":"单选框","title":"单选框","required":false,"optionList":[{"label":"选择项1","value":1},{"label":"选择项2","value":2},{"label":"选择项3","value":3},{"label":"选择项4","value":4}],"itemKey":"bede51613b868"},{"type":"DROPDOWN_SELECT","typeName":"下拉选择","title":"下拉选择","placeholder":"请选择","required":false,"optionList":[{"label":"选择项1","value":1},{"label":"选择项2","value":2},{"label":"选择项3","value":3},{"label":"选择项4","value":4}],"itemKey":"332bac8dd5964"},{"type":"RATING","typeName":"评分器","title":"评分器","required":false,"prefix":"","suffix":"","optionList":[{"label":"很不满意","value":1},{"label":"不满意","value":2},{"label":"一般","value":3},{"label":"满意","value":4},{"label":"很满意","value":5}],"itemKey":"483ba84a693aa"}]  
 */
```
统计选项占比
``` sql
select template_id,  
       title,  
       item_key,  
       label,  
       count(distinct id)                                       total,  
       sum(count)                                               count,  
       round(sum(count) / count(distinct id)::decimal, 4) * 100 radio  
from (select record.id,  
             record.template_id,  
             record.title,  
             record.value,  
             record.item_key,  
             record.label,  
             record.option,  
             iif(record.value = record.option, 1::int, 0::int) count,  
             sss.patient_name,  
             sss.patient_gender,  
             sss.patient_age,  
             sss.admitted_department,  
             sss.admitted_time,  
             sss.admitted_diagnosis,  
             sss.discharged_department,  
             sss.discharged_time,  
             sss.discharged_diagnosis,  
             sss.hospitalization_days,  
             sss.satisfaction,  
             sss.medical_record_num,  
             sss.is_deleted,  
             sss.record_no  
      from (select srr.id,  
                   srr.template_id,  
                   formSetting ->> 'title'                                                    title,  
                   formSetting,  
                   formSetting ->> 'itemKey'                                                  item_key,  
                   optionList,  
                   optionList ->> 'label'                                                     label,  
                   optionList ->> 'value'                                                      option,  
                   srr.scale_content::json -> 'formResult' ->> (formSetting ->> 'itemKey') as value  
            from scale_record_result srr,  
                 json_array_elements(scale_content::json -> 'formSetting') as formSetting,  
                 json_array_elements(formSetting -> 'optionList') as optionList  
  
            where formSetting ->> 'type' in ('RADIO_BUTTON', 'DROPDOWN_SELECT', 'RATING')  
              and srr.template_id = 1861583879303933953 ) record  
               left join scale_satisfaction_survey sss on record.id = sss.result_id) T where value  is not null  
group by template_id, title, item_key, label  
order by template_id, item_key
```