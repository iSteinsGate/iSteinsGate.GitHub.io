# IDEA TEMPLATE

## file and Code Templates

- 文件注释

```
/**
 *@author tanqinghui
 *@since ${YEAR}/${MONTH}/${DAY}${HOUR}:${MINUTE}
**/
```

## live template

- 注释doc

```
/**
*$END$
*@authortanqinghui
*@since$date$ $time$
**/
```

- apii

```
@ApiModelProperty("$desc$")
private Integer $var$;
```

- apis

```
@ApiModelProperty("$desc$")
private String $var$;
```

- bud

cla → className()

```
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
@ApiModel(value = "$cla$", description = "$desc$")
```

- dao

do → suggestVariableName()

```text
@Resource
private $DO$Mapper $var$;

public $DO$ getByPrimary(String id){
    return $var$.getByPrimary(id);
}
public void insert($DO$ entity){
    $var$.insert(entity);
}
public void updateSelective($DO$ entity){
    $var$.updateSelective(entity);
}
public void deleteByPrimary(String id){
    $var$.deleteByPrimary(id);
}
```

- ofCode

obj → className()

```TEXT
private String code;
private String name;

public static $obj$ ofCode(String code) {
    return Arrays.stream($obj$.values()).filter(item -> item.getCode().equals(code)).findFirst().orElse(null);
}
```

- get

```text
@GetMapping("$VAR$")
@ApiOperation("$DESC$")
public R $VAR$($END$) {
   return R.success();
}
```

- post

```text
@PostMapping("$VAR$")
@ApiOperation("$DESC$")
public R $VAR$($END$) {
   return R.success();
}
```

- rest

```text
@Resource
private $service$Service $serviceVar$;

@GetMapping("page")
@ApiOperation("分页查询")
public R<List<$service$BO>> page() {
    return R.data($serviceVar$.page());
}

@PostMapping("save")
@ApiOperation("保存")
public R save(@RequestBody @Valid $service$Save save) {
    $serviceVar$.save(save);
    return R.success();
}

@PostMapping("delete")
@ApiOperation("删除")
public R delete(@RequestBody @Valid IdBean<String> idBean) {
    $serviceVar$.delete(idBean.getId());
   return R.success();
}
```

## postfix Completion

- isBlank

```text
if(StringUtil.isBlank($EXPR$)){
   $END$
}
```

- nb
```text
if(StringUtil.isNotBlank($EXPR$)){
   $END$
}
```