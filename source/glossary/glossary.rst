.. _glossary:

==========
 术语
==========

.. 术语::

   buffer
      载入的文件内容和额外的元数据， 关联到一个或
      多个 views 。 *buffer* 和 *view* 的区别只是技术上的。
      大多数时候，这两个概念是等同的。

   view
      buffer 的图形显示。 多个 views 可以显示同一个 buffer 。

   plugin
      Python 实现的功能， 可能包含单一的 command
      或者多个 commands 。 可以写在一个 *.py* 文件或多个
      *.py* 文件中。

   package
      这个术语在 Sublime Text 可能有多重含义， 因为它可以是
      只一个 Python package （多数时候不是）， ``Packages`` 目录下的一个文件夹
      或者是一个 *.sublime-package* 文件。 大多数时候， 它是指
      ``Packages`` 目录下的一个文件夹，包含了一系列资源， 构成了
      一项新功能或为编程语言或标记语言提供了支持。

   panel
      一种输入输出组件， 例如 search panel 或者 output panel 。

   overlay
      一种特殊的输入组件。 例如， Goto Anything 就是一个 overlay。

   file type
      在 Sublime Text 中， *file type* 是指
      适用的 ``.tmLanguage`` 语法定义文件中定义的文件类型。

      然而，这是一个模糊的概念， 在某些情况下它
      所指会比技术文档中更广。

.. in extensibility/packages.rst:
   default packages
   shipped packages
   core packages
   user packages
   installed packages
   override packages
