#
#
# Copyright (c) 2015 - 2021 by blindtiger ( blindtiger@foxmail.com )
#
# The contents of this file are subject to the Mozilla Public License Version
# 2.0 (the "License"); you may not use this file except in compliance with
# the License. You may obtain a copy of the License at
# http://www.mozilla.org/MPL/
#
# Software distributed under the License is distributed on an "AS IS" basis,
# WITHOUT WARRANTY OF ANY KIND, either express or implied. SEe the License
# for the specific language governing rights and limitations under the
# License.
#
# The Initial Developer of the Original Code is blindtiger.
#
#

LABS=..\..\..\

!if "$(PLATFORM)" == "x86"
ARCTARG = I386
!if "$(PROCESSOR_ARCHITEW6432)" == "AMD64"
PATH = C:\Windows;C:\Windows\System32;$(LABS)\MSVC\Hostx64\x86
!else
PATH = C:\Windows;C:\Windows\System32;$(LABS)\MSVC\Hostx86\x86
!endif
!else if "$(PLATFORM)" == "x64"
ARCTARG = AMD64
!if "$(PROCESSOR_ARCHITEW6432)" == "AMD64"
PATH = C:\Windows;C:\Windows\System32;$(LABS)\MSVC\Hostx64\x64
!else
PATH = C:\Windows;C:\Windows\System32;$(LABS)\MSVC\Hostx86\x64
!endif
!else
!ERROR Usage: nmake (PLATFORM=x86|x64) (PROJ=ProjectName) (SLND=SolutionDir) [build|clean|rebuild]
!endif

BIND = $(SLND)Build\Bins\$(ARCTARG)

PROJTARG = $(BIND)\$(PROJ)
PROJD = $(SLND)Projects\$(PROJ)

BUILD: $(PROJTARG).sys

OBJD = $(SLND)Build\Objs\$(PROJ)\$(ARCTARG)

!if "$(ARCTARG)" == "I386"
PROJENTRY = GsDriverEntry@8
!else if "$(ARCTARG)" == "AMD64"
PROJENTRY = GsDriverEntry
!endif

ASOBJS = \
    $(OBJD)\$(ARCTARG).obj \
    $(OBJD)\OPS$(ARCTARG).obj

CCARCHOBJS = 

CCOBJS = \
    $(OBJD)\$(PROJ).obj \
    $(OBJD)\VmxHandler.obj \
    $(OBJD)\Vmx.obj
    
RCOBJS = $(OBJD)\$(PROJ).res

PROJOBJS = $(ASOBJS) $(CCARCHOBJS) $(CCOBJS) $(RCOBJS)

!if "$(ARCTARG)" == "I386"
TARGDEFS = /D_X86_=1 /Di386=1 /DSTD_CALL /DFPO=0 /DDOSWIN32 /DDETOURS_X86 /DDETOURS_32BIT
TARGAOPTS = /safeseh /coff /Zm
TARGCOPTS = /Gz /Gm- /EHs-c- /Od /Oy-
TARGLOPTS = 
HOTPATCH = /stub:$(LABS)\WRK\base\ntos\BUILD\PREBUILT\$(ARCTARG)\STUB512.com
ARCH = X86
ARCHML = ml
!else if "$(ARCTARG)" == "AMD64"
TARGDEFS = /D_WIN64 /D_AMD64_ /DAMD64 /DDETOURS_X64 /DDETOURS_64BIT
TARGAOPTS = 
TARGCOPTS = /Od /EHs-c- /Gs12288
TARGLOPTS = /IGNORE:4108,4088,4218,4218,4235
HOTPATCH = $(LABS)\WRK\base\ntos\BUILD\PREBUILT\$(ARCTARG)\HOTPATCH.obj
ARCH = AMD64
ARCHML = ml64
!endif

PROJLOPTS = /SUBSYSTEM:NATIVE /ENTRY:$(PROJENTRY) /DEF:$(PROJ).def

INCS = \
    /I$(LABS)\WRK\public\ddk\inc \
    /I$(LABS)\WRK\public\halkit\inc \
    /I$(LABS)\WRK\public\internal\base\inc \
    /I$(LABS)\WRK\public\internal\ds\inc \
    /I$(LABS)\WRK\public\internal\sdktools\inc \
    /I$(LABS)\WRK\public\sdk\inc \
    /I$(LABS)\WRK\public\sdk\inc\crt \
    /I$(LABS)\WRK\base\inc \
    /I$(LABS)\WRK\base\ntos\inc \
    /I$(LABS)\WinDDK\3790.1830\inc\wnet \
    /I$(LABS)\WinDDK\3790.1830\inc\crt\gl \
    /I$(SLND)\Include \
    /I$(PROJD)

DEFS = /DUNICODE /D_UNICODE $(TARGDEFS) /DCONDITION_HANDLING=1 /DNT_INST=0 /DWIN32=100 /D_NT1X_=100 /DWINNT=1 \
    /DDBG=1 /DDEVL=1 /DNDEBUG /D_NTSYSTEM_ /DNT_SMT /DNTOS_KERNEL_RUNTIME=1 /D_NTDRIVER_

COPTS = /Z7 /Zl /Zp8 /Gy /cbstring /W3 /WX /GR- /GF /GS $(TARGCOPTS)
COMPILERWARNINGS = /FI$(LABS)\WRK\base\ntos\BUILD\WARNING.h /FI$(SLND)\Include\WARNING.h

AOPTS = /Cx /Zi /Zd $(TARGAOPTS)
AS = $(ARCHML).exe /nologo
AFLAGS = $(AOPTS) $(INCS) /Fo$(OBJD)\ $(DEFS) $(SPECIALAFLAGS)

CC = cl.exe /nologo
CFLAGS = $(COPTS) $(INCS) /Fo$(OBJD)\ $(DEFS) $(SPECIALCFLAGS) $(COMPILERWARNINGS)

RC = rc.exe /nologo
RFLAGS = $(INCS) /fo$(RCOBJS)

LINKLIBPATH = \
    /LIBPATH:$(LABS)\WinDDK\7600.16385.1\lib\win7\$(ARCTARG) \
    /LIBPATH:$(LABS)\WinDDK\7600.16385.1\lib\Crt\$(ARCTARG) \
    /LIBPATH:$(SLND)\Lib\$(ARCTARG)

LINKIGNORE = /IGNORE:4087,4001,4010,4037,4039,4065,4070,4078,4087,4089,4221,4198
BUILDLIBS = hal.lib ntoskrnl.lib bufferoverflowk.lib \
!if "$(ARCTARG)" == "I386"
    \
!else if "$(ARCTARG)" == "AMD64"
    gshandler.obj gshandlerseh.obj \
!endif

LINKFLAGS = $(LINKIGNORE) /WX /NODEFAULTLIB /machine:$(ARCH) /debug /debugtype:cv,fixup
LINK = link.exe /nologo

LINKPROJNAMES = /out:$(PROJTARG).sys /map:$(PROJTARG).map /pdb:$(PROJTARG).pdb

$(PROJTARG).sys: $(PROJOBJS)
    @$(LINK) $(LINKFLAGS) $(PROJLOPTS) $(LINKPROJNAMES) $** $(HOTPATCH) $(LINKLIBPATH) $(BUILDLIBS)
    @if not exist $(BIND)\Start.cmd copy /Y $(SLND)\Start.cmd $(BIND)\Start.cmd
    @if not exist $(BIND)\Stop.cmd copy /Y $(SLND)\Stop.cmd $(BIND)\Stop.cmd

# assembly files
{$(ARCTARG)\}.asm{$(OBJD)\}.obj:
    @$(AS) $(AFLAGS) /c $<

# arch-specific C files
{$(ARCTARG)\}.c{$(OBJD)\}.obj:
    @$(CC) $(CFLAGS) /c $<

# C files
.c{$(OBJD)\}.obj:
    @$(CC) $(CFLAGS) /c $<
    
# RC files
.rc{$(OBJD)\}.res::
    @$(RC) $(RFLAGS) $<

# CLEAN pseudo targets

CLEAN:
    @if exist $(OBJD)\*.res del /F /Q $(OBJD)\*.res
    @if exist $(OBJD)\*.obj del /F /Q $(OBJD)\*.obj
    @if exist $(PROJTARG).exe del /F /Q $(PROJTARG).exe
    @if exist $(PROJTARG).sys del /F /Q $(PROJTARG).sys
    @if exist $(PROJTARG).dll del /F /Q $(PROJTARG).dll
    @if exist $(PROJTARG).lib del /F /Q $(PROJTARG).lib
    @if exist $(PROJTARG).sys del /F /Q $(PROJTARG).sys
    @if exist $(PROJTARG).exp del /F /Q $(PROJTARG).exp
    @if exist $(PROJTARG).map del /F /Q $(PROJTARG).map
    @if exist $(PROJTARG).pdb del /F /Q $(PROJTARG).pdb

REBUILD: CLEAN BUILD
