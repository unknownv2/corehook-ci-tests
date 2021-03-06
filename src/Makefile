##############################################################################
##
##  Makefile for CoreHook Binary Hooking Module.
##
##  Based on Microsoft Research Detours Package, Version 4.0.1
##

ROOT = ..
!include "$(ROOT)\system.mak"

!IF "$(DETOURS_SOURCE_BROWSING)" == ""
DETOURS_SOURCE_BROWSING = 0
!ENDIF

#######################/#######################################################
##
CFLAGS=/W4 /WX /Zi /MT /Gy /Gm- /Zl /Od

!IF $(DETOURS_SOURCE_BROWSING)==1
CFLAGS=$(CFLAGS) /FR
!ELSE
CFLAGS=$(CFLAGS) /DWIN32_LEAN_AND_MEAN /D_WIN32_WINNT=0x600
!ENDIF

!IF "$(DETOURS_TARGET_PROCESSOR)" == "IA64"
CFLAGS=$(CFLAGS) /wd4163 # intrinsic rdtebex not available; using newer Windows headers with older compiler
!ENDIF

!if defined(DETOURS_WIN_7) && defined(DETOURS_CL_17_OR_NEWER)
CFLAGS=$(CFLAGS) /D_USING_V110_SDK71_
!elseif defined(DETOURS_ANALYZE)
CFLAGS=$(CFLAGS) /analyze
!endif

!IF "$(DETOURS_TARGET_PROCESSOR)" == "X64"
ASM_SOURCES=trampolinex64.asm
ML=ml64
!ELSEIF "$(DETOURS_TARGET_PROCESSOR)" == "X86"
ASM_SOURCES=trampolinex86.asm
ML=ml
!ELSEIF "$(DETOURS_TARGET_PROCESSOR)" == "ARM"
ASM_SOURCES=trampolinearm.asm
ARMASM=armasm
!ELSEIF "$(DETOURS_TARGET_PROCESSOR)" == "ARM64"
ASM_SOURCES=trampolinearm64.asm
ARMASM64=armasm64
!ENDIF

OBJS = \
    $(OBJD)\detours.obj     \
    $(OBJD)\modules.obj     \
    $(OBJD)\disasm.obj      \
    $(OBJD)\image.obj       \
    $(OBJD)\creatwth.obj    \
    $(OBJD)\disolx86.obj    \
    $(OBJD)\disolx64.obj    \
    $(OBJD)\disolia64.obj   \
    $(OBJD)\disolarm.obj    \
    $(OBJD)\disolarm64.obj  \
    $(OBJD)\barrier.obj     \
!IF "$(DETOURS_TARGET_PROCESSOR)" == "X64"    
    $(OBJD)\trampolinex64.obj \
!ELSEIF "$(DETOURS_TARGET_PROCESSOR)" == "X86"    
    $(OBJD)\trampolinex86.obj \
!ELSEIF "$(DETOURS_TARGET_PROCESSOR)" == "ARM"    
    $(OBJD)\trampolinearm.obj \
!ELSEIF "$(DETOURS_TARGET_PROCESSOR)" == "ARM64"    
    $(OBJD)\trampolinearm64.obj \
!ENDIF

##############################################################################
##
.SUFFIXES: .asm .cpp .h .obj

!ifdef DETOURS_ANALYZE
.cpp{$(OBJD)}.obj:
    $(CC) $(CFLAGS) /Fd$(LIBD)\detours.pdb /Fo$(OBJD)\ /c $<
!else
.cpp{$(OBJD)}.obj::
    $(CC) $(CFLAGS) /Fd$(LIBD)\detours.pdb /Fo$(OBJD)\ /c $<
!endif

##############################################################################

all: dirs \
    $(LIBD)\detours.lib \
    $(INCD)\detours.h   \
    $(INCD)\detver.h    \
!IF $(DETOURS_SOURCE_BROWSING)==1
    $(OBJD)\detours.bsc \
!endif

##############################################################################

clean:
    -del *~ 2>nul
    -del $(LIBD)\detours.pdb $(LIBD)\detours.lib 2>nul
    -rmdir /q /s $(OBJD) 2>nul

realclean: clean
    -rmdir /q /s $(OBJDS) 2>nul

##############################################################################

dirs:
    @if not exist "$(INCD)" mkdir "$(INCD)" && echo.   Created $(INCD)
    @if not exist "$(LIBD)" mkdir "$(LIBD)" && echo.   Created $(LIBD)
    @if not exist "$(BIND)" mkdir "$(BIND)" && echo.   Created $(BIND)
    @if not exist "$(OBJD)" mkdir "$(OBJD)" && echo.   Created $(OBJD)

$(OBJD)\detours.bsc : $(OBJS)
    bscmake /v /n /o $@ $(OBJS:.obj=.sbr)

$(LIBD)\detours.lib : $(OBJS)
    link /lib /out:$@ $(OBJS)

$(INCD)\detours.h : detours.h
    copy detours.h $@

$(INCD)\detver.h : detver.h
    copy detver.h $@

$(OBJD)\detours.obj : detours.cpp detours.h
$(OBJD)\modules.obj : modules.cpp detours.h
$(OBJD)\disasm.obj : disasm.cpp detours.h
$(OBJD)\image.obj : image.cpp detours.h
$(OBJD)\creatwth.obj : creatwth.cpp uimports.cpp detours.h
$(OBJD)\disolx86.obj: disasm.cpp detours.h
$(OBJD)\disolx64.obj: disasm.cpp detours.h
$(OBJD)\disolia64.obj: disasm.cpp detours.h
$(OBJD)\disolarm.obj: disasm.cpp detours.h
$(OBJD)\disolarm64.obj: disasm.cpp detours.h
$(OBJD)\barrier.obj: barrier.cpp detours.h
!IF "$(DETOURS_TARGET_PROCESSOR)" == "X64"
$(OBJD)\trampolinex64.obj: trampolinex64.asm
     $(ML) /c /Sn /Fo $(OBJD)\trampolinex64.obj /nologo $(ASM_SOURCES)
!ELSEIF "$(DETOURS_TARGET_PROCESSOR)" == "X86"
$(OBJD)\trampolinex86.obj: trampolinex86.asm
     $(ML) /c /Sn /Fo $(OBJD)\trampolinex86.obj /nologo $(ASM_SOURCES)
!ELSEIF "$(DETOURS_TARGET_PROCESSOR)" == "ARM"
$(OBJD)\trampolinearm.obj: $(ASM_SOURCES)
     $(ARMASM) -o $(OBJD)\trampolinearm.obj $(ASM_SOURCES)
!ELSEIF "$(DETOURS_TARGET_PROCESSOR)" == "ARM64"
$(OBJD)\trampolinearm64.obj: $(ASM_SOURCES)
     $(ARMASM64) -o $(OBJD)\trampolinearm64.obj $(ASM_SOURCES)
!ENDIF

test: all
    cd $(MAKEDIR)\..\samples\slept
    nmake /nologo test
    cd $(MAKEDIR)

################################################################# End of File.
