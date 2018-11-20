Current EDK II supports following kinds of address sanitizer features:
1. Page/pool memory overflow detection (Heap Guard)
    * PcdHeapGuardPropertyMask
    * PcdHeapGuardPoolType
    * PcdHeapGuardPageType
2. NULL pointer access detection (NULL Detection)
    * PcdNullPointerDetectionPropertyMask
3. Use-After-Free page/pool memory detection (UAF Detection)
    * PcdHeapGuardPropertyMask
4. Global stack overflow detection (Stack Guard)
    * PcdCpuStackGuard
5. Local stack overflow detection (BaseStackCheckLib)
    * -fstack-protector-all (MdePkg\Library\BaseStackCheckLib\BaseStackCheckLib.inf)

# Heap Guard
to-be-done