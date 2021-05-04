---
title: OEM安装卸载驱动
date: 2019-05-06 20:00:48
categories: 
 - C#
tags:
 - Driver
 - SetupApi
---
介绍使用SetupApi的SetupCopyOEMInf与SetupUninstallOEMInf安装卸载驱动的方法

<!-- more -->

## 方法原型
SetupCopyOEMInfW [https://docs.microsoft.com/en-us/windows/win32/api/setupapi/nf-setupapi-setupcopyoeminfw][1]
SetupUninstallOEMInfA [https://docs.microsoft.com/en-us/windows/win32/api/setupapi/nf-setupapi-setupuninstalloeminfa][2]
``` x86asm
WINSETUPAPI BOOL SetupCopyOEMInfW(
  PCWSTR SourceInfFileName, //Full path to the source .inf file.
  PCWSTR OEMSourceMediaLocation,
  DWORD  OEMSourceMediaType,
  DWORD  CopyStyle,
  PWSTR  DestinationInfFileName,
  DWORD  DestinationInfFileNameSize,
  PDWORD RequiredSize,
  PWSTR  *DestinationInfFileNameComponent
);
WINSETUPAPI BOOL SetupUninstallOEMInfA(
  PCSTR InfFileName,
  DWORD Flags,
  PVOID Reserved
);
```

## 导入
``` cs
[DllImport("setupapi.dll", SetLastError = true)]
public static extern bool SetupCopyOEMInf(
    string SourceInfFileName,
    string OEMSourceMediaLocation,
    OemSourceMediaType OEMSourceMediaType,
    OemCopyStyle CopyStyle,
    StringBuilder DestinationInfFileName,
    int DestinationInfFileNameSize,
    ref int RequiredSize,
    out string DestinationInfFileNameComponent
);
[DllImport("setupapi.dll", SetLastError = true)]
public static extern bool SetupUninstallOEMInf(
    string InfFileName,
        int Flags,
        IntPtr Reserved
    );
/// <summary>
/// Driver media type
/// </summary>
public enum OemSourceMediaType
{
    SPOST_NONE = 0,
    //Only use the following if you have a pnf file as well
    SPOST_PATH = 1,
    SPOST_URL = 2,
    SPOST_MAX = 3
}
public enum OemCopyStyle
{
    SP_COPY_NEWER = 0x0000004,   // copy only if source newer than or same as target
    SP_COPY_NEWER_ONLY = 0x0010000,   // copy only if source file newer than target
    SP_COPY_OEMINF_CATALOG_ONLY = 0x0040000,   // (SetupCopyOEMInf only) don't copy INF--just catalog
}
```

## 安装
使用SetupCopyOEMInf安装成功后，会在`C:\Windows\INF`目录下生成`oem63.inf` `oem63.PNF` 驱动文件，即参数desInfFileNameComp返回的数据
``` nimrod
    bool success = false;
    string desInfFileNameComp = "";
    StringBuilder desInfFileName_builder = new StringBuilder(260);
    unsafe
    {
        success = SetupCopyOEMInf(fileName, null, OemSourceMediaType.SPOST_PATH, OemCopyStyle.SP_COPY_NEWER, desInfFileName_builder, desInfFileName_builder.Capacity,
                        ref size, out desInfFileNameComp);
    }           
    Console.WriteLine(desInfFileNameComp);
    if (!success)
    {
        var errorCode = Marshal.GetLastWin32Error();
        var errorString = new Win32Exception(errorCode).Message;
        Console.WriteLine(fileName + " Install fail：" + errorString);
    }
```

## 卸载
会删除`C:\Windows\INF`目录下生成`oem63.inf` `oem63.PNF` 驱动文件，即参数desInfFileNameComp返回的数据
``` nimrod
    bool success = false;
    string desInfFileNameComp = "";
    StringBuilder desInfFileName_builder = new StringBuilder(260);
    unsafe
    {
        success = SetupCopyOEMInf(fileName, null, OemSourceMediaType.SPOST_PATH, OemCopyStyle.SP_COPY_OEMINF_CATALOG_ONLY, desInfFileName_builder, desInfFileName_builder.Capacity,
                        ref size, out desInfFileNameComp);
    }           
    Console.WriteLine(desInfFileNameComp);
    if (!success)
    {
        var errorCode = Marshal.GetLastWin32Error();
        var errorString = new Win32Exception(errorCode).Message;
        Console.WriteLine(fileName + " unInstall fail：" + errorString);
    }
    else
    {
        unsafe
        {
            IntPtr Reserved = new IntPtr();
            success = SetupUninstallOEMInf(desInfFileNameComp, 1, Reserved);
        }
        if (!success)
        {
            var errorCode = Marshal.GetLastWin32Error();
            var errorString = new Win32Exception(errorCode).Message;
            Console.WriteLine(fileName + " unInstall fail：" + errorString);
        }
        else
        {
            Console.WriteLine(fileName + " unInstall success");            
	}
    }    
```
## 错误记录
1. 错误码：`0xe000024b`
文件的哈希不存在于指定的目录文件中。该文件可能已损坏或是篡改的受害者。


  [1]: https://docs.microsoft.com/en-us/windows/win32/api/setupapi/nf-setupapi-setupcopyoeminfw
  [2]: https://docs.microsoft.com/en-us/windows/win32/api/setupapi/nf-setupapi-setupuninstalloeminfa
