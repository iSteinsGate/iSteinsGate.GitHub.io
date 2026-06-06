
-pl, --projects
    构建指定的模块，模块间用逗号分隔；适合无依赖的项目
-am, --also-make (常用)
    同时构建所列模块的依赖模块，比如A依赖B，B依赖C，构建B，同时构建C
-amd, --also-make-dependents
        同时构建依赖于所列模块的模块，比如A依赖B，B依赖C,构建B，同时构建A


```

mvn versions:set -DnewVersion=${nghis_integration_version}
mvn -U clean deploy -Dmaven.test.skip=true -Dautoconfig.skip
```


```
mvn clean install -pl 子模块1 -am -amd


mvn -U clean deploy -pl nghis-applet-boot -am -Dmaven.test.skip=true -Dautoconfig.skip
```