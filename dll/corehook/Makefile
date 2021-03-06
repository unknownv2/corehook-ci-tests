!include ..\common.mak

CFLAGS = $(CFLAGS:/Od=/O2)

LIBS=$(LIBS) kernel32.lib aux_ulib.lib

##############################################################################

all: dirs \
    $(BIND)\corehook$(DETOURS_BITS).dll \
    \
!IF $(DETOURS_SOURCE_BROWSING)==1
    $(OBJD)\corehook$(DETOURS_BITS).bsc \
!ENDIF
    option

##############################################################################

dirs:
    @if not exist $(BIND) mkdir $(BIND) && echo.   Created $(BIND)
    @if not exist $(OBJD) mkdir $(OBJD) && echo.   Created $(OBJD)


$(OBJD)\corehook.obj : corehook.cpp

$(OBJD)\corehook.res : corehook.rc

$(BIND)\corehook$(DETOURS_BITS).dll $(BIND)\corehook$(DETOURS_BITS).lib: \
        $(OBJD)\corehook.obj $(OBJD)\corehook.res $(DEPS)
    cl /LD $(CFLAGS) /Fe$(@R).dll /Fd$(@R).pdb \
        $(OBJD)\corehook.obj $(OBJD)\corehook.res \
        /link $(LINKFLAGS) /subsystem:console \
        /export:DetourFinishHelperProcess,@1,NONAME \
        /export:DetourAttach \
        /export:DetourTransactionBegin \
        /export:DetourTransactionAbort \
        /export:DetourCreateProcessWithDllsW \
        /export:DetourCreateProcessWithDllsA \
        /export:DetourCreateProcessWithDllExW \
        /export:DetourCreateProcessWithDllExA \
        /export:DetourCopyPayloadToProcess \
        /export:DetourUpdateThread \
        /export:DetourTransactionCommit \
        /export:DetourGetHookHandleForFunction \
        /export:DetourSetCallbackForLocalHook \
        /export:DetourDetach \
        /export:DetourEnumerateExports \
        /export:DetourEnumerateModules \
        /export:DetourEnumerateImports \
        /export:DetourFindFunction \
        /export:DetourIsHelperProcess \
        /export:DetourRestoreAfterWith \
        /export:DetourGetEntryPoint \
        /export:DetourCriticalInitialize \
        /export:DetourInstallHook \
        /export:DetourUninstallHook \
        /export:DetourBarrierGetCallback \
        /export:DetourSetExclusiveACL \
        /export:DetourSetInclusiveACL \
        /export:DetourSetGlobalExclusiveACL \
        /export:DetourSetGlobalInclusiveACL \
        /export:DetourIsThreadIntercepted \
        /export:DetourBarrierProcessAttach \
        /export:DetourGetHookBypassAddress \
        /export:DetourBarrierGetReturnAddress \
        /export:DetourBarrierGetAddressOfReturnAddress \
        /export:DetourBarrierBeginStackTrace \
        /export:DetourBarrierEndStackTrace \
        /export:DetourBarrierCallStackTrace \
        $(LIBS)

$(OBJD)\corehook$(DETOURS_BITS).bsc : $(OBJD)\corehook.obj
    bscmake /v /n /o $@ $(OBJD)\corehook.sbr

##############################################################################

clean:
    -del *~ 2>nul
    -del $(BIND)\corehook*.* 2>nul
    -rmdir /q /s $(OBJD) 2>nul

realclean: clean
    -rmdir /q /s $(OBJDS) 2>nul

############################################### Install non-bit-size binaries.

!IF "$(DETOURS_OPTION_PROCESSOR)" != ""

$(OPTD)\corehook$(DETOURS_OPTION_BITS).dll:
$(OPTD)\corehook$(DETOURS_OPTION_BITS).pdb:

$(BIND)\corehook$(DETOURS_OPTION_BITS).dll: $(OPTD)\corehook$(DETOURS_OPTION_BITS).dll
    @if exist $? copy /y $? $(BIND) >nul && echo $@ copied from $(DETOURS_OPTION_PROCESSOR).
$(BIND)\corehook$(DETOURS_OPTION_BITS).pdb: $(OPTD)\corehook$(DETOURS_OPTION_BITS).pdb
    @if exist $? copy /y $? $(BIND) >nul && echo $@ copied from $(DETOURS_OPTION_PROCESSOR).

option: \
    $(BIND)\corehook$(DETOURS_OPTION_BITS).dll \
    $(BIND)\corehook$(DETOURS_OPTION_BITS).pdb \

!ELSE

option:

!ENDIF

##############################################################################

test: all
    @echo -------- Test completed. ------------------------------------------------

################################################################# End of File.
