Waterfalls
========================================================

Nearly all applications, web servers, and web application environments are vulnerable to buffer overflows. Environments that are written in interpreted languages, such as Java and Python, are immune to the attacks, with the exception of overflows in their interpreter. These issues are particularly problematic in the programming language C/C++ as it does not have buffer overflow protection built in.

Buffer overflows are used in many of the exploits against vulnerable services. Most of these are pre-compiled by exploit developers. To be able to generate payloads locally, set up a Kali VM with the necessary tools, and a Windows VM with Immunity Debugger (on the same network).

.. image:: _static/images/in-progress.png
  :alt: Forever in progress ...

----

.. toctree::
   :maxdepth: 1
   :includehidden:
   :caption: Testlab

   Virtual machines <https://testlab.tymyrddin.dev/pdocs/vm/README>
   Assemblers <https://testlab.tymyrddin.dev/docs/asm/README>
   Compilers <https://testlab.tymyrddin.dev/docs/com/README>
   Disassemblers <https://testlab.tymyrddin.dev/docs/dis/README>
   Debuggers <https://testlab.tymyrddin.dev/docs/deb/README>
   Decompilers <https://testlab.tymyrddin.dev/docs/dec/README>
   Exploit development <https://testlab.tymyrddin.dev/docs/edt/README>
   Exploitation tools <https://testlab.tymyrddin.dev/docs/exploitation/README>

----

.. toctree::
   :maxdepth: 1
   :includehidden:
   :caption: Notes on techniques

   docs/notes/README.md
   docs/notes/patterns.md
   docs/notes/linux-x86.md

----

.. toctree::
   :maxdepth: 1
   :includehidden:
   :caption: Coding

   Windows <https://github.com/tymyrddin/codes-bof/tree/main/windows>
   Linux <https://github.com/tymyrddin/codes-bof/tree/main/linux>

----

.. toctree::
   :maxdepth: 1
   :includehidden:
   :caption: TryHackMe

   docs/thm/README.md
   docs/thm/overflow1.md
   docs/thm/overflow10.md
   docs/thm/brainstorm.md
   docs/thm/gatekeeper.md

.. toctree::
   :glob:
   :maxdepth: 1
   :includehidden:
   :caption: Root-me

   docs/root-me/README.md
   docs/root-me/elf-86-stack-basic1.md
   docs/root-me/elf-64-heap-basic1.md
   docs/root-me/elf-86-stack-basic2.md
   docs/root-me/elf-86-string-basic1.md
   docs/root-me/elf-64-stack-basic.md
   docs/root-me/elf-86-string-basic2.md
   docs/root-me/elf-86-race-condition.md
   docs/root-me/elf-arm-stack-basic.md
   docs/root-me/elf-86-stack-basic3.md
   docs/root-me/elf-86-free-basic.md
   docs/root-me/elf-86-bss.md
   docs/root-me/elf-86-stack-basic4.md
   docs/root-me/elf-86-stack-basic6.md
   docs/root-me/elf-86-string-basic3.md
   docs/root-me/elf-arm-basic-rop.md
   docs/root-me/elf-86-stack-cpp.md
   docs/root-me/elf-86-stack-basic5.md
   docs/root-me/elf-86-remote-string.md

----

.. image:: _static/images/books.png
  :alt: Useful books
