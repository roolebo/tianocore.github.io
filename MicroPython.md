> MicroPython is a lean and efficient implementation of the Python 3 programming language that includes a small subset of the Python standard library and is optimised to run on microcontrollers and in constrained environments.
-- [micropython.org](https://micropython.org/)

A port of the MicroPython Interpreter for UEFI is available in edk2-staging: (`MicroPythonPkg`)

https://github.com/tianocore/edk2-staging/tree/MicroPythonTestFramework/MicroPythonPkg

This port was developed as a component of the [[MicroPython Test Framework for UEFI]], designed for firmware unit testing and validation. It is also under evaluation to replace the existing [Python 2.7 port for UEFI](https://github.com/tianocore/edk2/tree/master/AppPkg/Applications/Python).

This project was [publicly announced in March 2018](https://software.intel.com/en-us/blogs/2018/03/08/implementing-micropython-as-a-uefi-test-framework) and added to the [edk2-staging](https://github.com/tianocore/edk2-staging) branch in August 2018.