.. sublime: wordWrap false

语法定义
==================


.. note::

    在 Sublime Text Build 3084 中，
    添加了一种新的语法定义格式，
    使用 ``.sublime-syntax`` 扩展名。

    相关内容参看：
    http://www.sublimetext.com/docs/3/syntax.html


Textmate 兼容的语法定义
***************************

通常， Sublime Text 的语法定义是与 Textmate
兼容的。

文件格式
***********

Textmate 语法定义文件是 Plist 文件，使用 ``tmLanguage`` 扩展名。
然而， 为了方便，这里使用 YAML 格式。

另外， Sublime Text 也支持 ``hidden-tmLanguage`` 扩展名。
这种格式不能被用户选择，但是可以被插件利用。 "Find in
Files" 利用了这种特性。 这种方式的缺点在于不能被其他语法定义文件使用导入语法包含。

.. code-block:: yaml

    ---
    name: Sublime Snippet (Raw)
    scopeName: source.ssraw
    fileTypes: [ssraw]
    uuid: 0da65be4-5aac-4b6f-8071-1aadb970b8d9

    patterns:
    - comment: Tab stops like $1, $2...
      name: keyword.other.ssraw
      match: \$\d+

    - comment: Variables like $PARAM1, $TM_SELECTION...
      name: keyword.other.ssraw
      match: \$([A-Za-z][A-Za-z0-9_]+)
      captures:
        '1': {name: constant.numeric.ssraw}

    - name: variable.complex.ssraw
      begin: '(\$)(\{)([0-9]+):'
      beginCaptures:
        '1': {name: keyword.other.ssraw}
        '3': {name: constant.numeric.ssraw}
      end: \}
      patterns:
      - include: $self
      - name: support.other.ssraw
        match: .

    - name: constant.character.escape.ssraw
      match: \\[$<>]

    - name: invalid.illegal.ssraw
      match: '[$<>]'
    ...


``name``
    语法定义的名称。 显示在 Sublime Text 右下角的语法选择下拉菜单里。
    通常就是编程语言的名称。

``scopeName``
    最顶层的语法定义作用域。 可以为
    ``source.<lang>`` 或者 ``text.<lang>``。 使用 ``source`` 标明编程语言，使用 ``text``
    标明标记语言和其他类型。

``fileTypes``
    这里是一系列文件扩展名 （不带点号）。 当打开
    这些类型的文件时， Sublime Text 会自动为它们选择语法文件。 可选的。

``uuid``
    语法定义的唯一标识。 目前可以忽略。

``patterns``
    用于匹配 buffer 中文本的模式列表。

``repository``
    Array of patterns abstracted out from the ``patterns`` element. Useful to
    keep the syntax definition tidy as well as for specialized uses like
    recursive patterns or re-using the same pattern. Optional.


The Patterns Array
******************

Elements contained in the ``patterns`` array.

``match``
    Contains the following elements:

    ============    ============================================================
    ``match``       Pattern to search for.
    ``name``        Optional. Scope name to be assigned to matches of ``match``.
    ``comment``     Optional. For information only.
    ``captures``    Optional. Refinement of ``match``. See below.
    ============    ============================================================

    In turn, ``captures`` can contain *n* of the following pairs of elements
    (note that ``0`` refers to the whole match):

    ========      ===============================================
    ``0..n``      Name of the group referenced. Must be a string.
    ``name``      Scope to be assigned to the group.
    ========      ===============================================

    Examples:

    .. code-block:: yaml

        # Simple

        - comment: Sequences like \$, \> and \<
          name: constant.character.escape.ssraw
          match: \\[$<>]

        # With captures

        - comment: Tab stops like $1, $2...
          name: keyword.other.ssraw
          match: \$(\d+)
          captures:
            '1': {name: constant.numeric.ssraw}

``include``
    Includes items in the repository, other syntax definitions or the current
    one.

    References:

        =========       ===========================
        $self           The current syntax definition.
        #itemName       itemName in the repository.
        source.js       External syntax definitions.
        =========       ===========================

    Examples:

    .. code-block:: yaml

        # Requires presence of DoubleQuotedStrings element in the repository.
        - include: '#DoubleQuotedStrings'

        # Recursively includes the complete current syntax definition.
        - include: $self

        # Includes and external syntax definition.
        - include: source.js

``begin..end``
    Defines a scope potentially spanning multiple lines

    Contains the following elements (only ``begin`` and ``end`` are required):

        =================   ====================================================
        ``name``            Scope name for the content including the markers.
        ``contentName``     Scope name for the content excluding the markers.
        ``begin``           The start marker pattern.
        ``end``             The end marker pattern.
        ``name``            Scope name for the whole region.
        ``beginCaptures``   ``captures`` for ``begin``. See ``captures``.
        ``endCaptures``     ``captures`` for ``end``. See ``captures``.
        ``patterns``        Array of patterns to be matched against the content.
        =================   ====================================================

    Example:

    .. code-block:: yaml

        name: variable.complex.ssraw
        begin: '(\$)(\{)([0-9]+):'
        beginCaptures:
          '1': {name: keyword.other.ssraw}
          '3': {name: constant.numeric.ssraw}
        end: \}
        patterns:
        - include: $self
        - name: support.other.ssraw
          match: .

Repository
**********

Can be referenced from ``patterns`` or from itself in an ``include`` element.
See ``include`` for more information.

The repository can contain the following elements:

.. code-block:: yaml

    repository:

      # Simple elements
      elementName:
        match: some regexp
        name:  some.scope.somelang

      # Complex elements
      otherElementName:
        patterns:
        - match: some regexp
          name:  some.scope.somelang
        - match: other regexp
          name:  some.other.scope.somelang

Examples:

.. code-block:: js

    repository:
      numericConstant:
        patterns:
        - name: constant.numeric.double.powershell
          match: \d*(?<!\.)(\.)\d+(d)?(mb|kb|gb)?
          captures:
            '1': {name: support.constant.powershell}
            '2': {name: support.constant.powershell}
            '3': {name: keyword.other.powershell}
        - name: constant.numeric.powershell
          match: (?<!\w)\d+(d)?(mb|kb|gb)?(?!\w)
          captures:
            '1': {name: support.constant.powershell}
            '2': {name: keyword.other.powershell}

      scriptblock:
        name: meta.scriptblock.powershell
        begin: \{
        end: \}
        patterns:
        - include: $self


Escape Sequences
****************

Be sure to escape JSON/XML sequences as needed.

.. EXPLAIN

For YAML, additionally make sure that you didn't unintentionally start a new
scalar by not using quotes for your strings. Examples that **won't work** as
expected::

    match: [aeiou]

    include: #this-is-actually-a-comment

    match: "#"\w+""
