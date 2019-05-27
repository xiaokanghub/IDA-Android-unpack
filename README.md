# IDA-Android-脱壳
##Android 脱壳<br>
    int dvmDexFileOpenPartial(const void* addr, int len, DvmDex** ppDvmDex);第一个参数就是dex内存起始地址，第二个参数就是dex大小<br>
    将dbgsrv目录下的android_server，复制到手机/data/local/tmp/目录下，并添加777权限。如果你的手机是64系统，请复制android_server64，如果使用模拟机，请复制android_x86_server<br>
    adb shell >> ./data/local/tmp/android-server<br>
    adb forward tcp:23946 tcp:23946<br>
    adb shell am start -D -n com.ali.tg.testapp/.MainActivity or adb shell am set-debug-app -w com.qihoo.camera<br>
    adb forward tcp:8700 jdwp:14243(PID) or adb forward tcp:pid jdwp:pid <br>
    IDA debugger attach<br>
    jdb -connect com.sun.jdi.SocketAttach:hostname=127.0.0.1,port=8700 or jdb -attach 127.0.0.1:pid<br>
    dvmDexFileOpenPartial下断DUMP<br>
    ```
    static main(void){
          auto fp, dex_addr, end_addr;
          fp = fopen("F:\\dump.dex", "wb");  
          end_addr = r0 + r1;   
          for ( dex_addr=r0; dex_addr < end_addr; dex_addr ++ ) 
          fputc(Byte(dex_addr), fp);
          }
          ```<br>
          以上是Dalvik模式(Android <= 4.2),ART模式(Android >= 5.0)：attach成功后，打开modules 搜索libart.so，然后在libart.so中搜索Openmemory函数并且跟进去。然后下断点。寄存器R1保存dex起始位，寄存器R2保存dex大小,脱壳脚本中需要R0改成R1，R1改成R2即可<br>
#数字壳<br>
    [文献](https://blog.zimperium.com/dissecting-mobile-native-code-packers-case-study/)<br>
    
    

```IDC
static main(void)
{
    auto fp, dex_addr, end_addr;
    fp = fopen(“F:\\dump.dex”, “wb”);
    end_addr = r0 + r1;
    for ( dex_addr = r0; dex_addr < end_addr; dex_addr ++ )
        fputc(Byte(dex_addr), fp);
}
```
          
          
          
          
          

    
