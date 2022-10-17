
# CMake LINK Debug or Release

## Introduction

    CMakeにて、リンクする際にDebug/Releaseを切り替える方法。  
    
## やり方

``` cmake
target_link_libraries(
  ${PROJECT_NAME}
  debug ${OPENAL_DEBUG}/common.lib
  optimized ${OPENAL_RELEASE}/common.lib
)
```

    debug/optimized/generalから選択が出来ます。  
    debug : Debug構成の場合に適用します。  
    optimized : Debug構成以外の場合に適用します。  
    general : Debug構成を含めたすべてで適用します。  
    

