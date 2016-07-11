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
    从 ``patterns`` 元素中抽取的模式列表。 可以使语法定义文件保持简洁。
    同时可以用于模式递归和复用。可选的。


模式列表
******************

``patterns`` 列表中包含的元素。

``match``
    包含如下元素:

    ============    ============================================================
    ``match``       要搜索的模式。
    ``name``        可选的。 ``match`` 的作用域名称。
    ``comment``     可选的。 注释。
    ``captures``    可选的。 ``match`` 提取元素。 见下面。
    ============    ============================================================

    另外， ``captures`` 可以包含 *n* 个如下的元素。
    （注意， ``0`` 指的是整个分组）:

    ========      ===============================================
    ``0..n``      分组名称。 必须是字符串。
    ``name``      分组的作用域名称。
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
    从库中，其他语法定义文件，或当前文件中导入项目。

    参考:

        =========       ===========================
        $self           当前语法定义。
        #itemName       库中的项目名。
        source.js       外部语法定义文件。
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
    定义一个可以跨越多行的作用域。

    包含如下元素 （只有 ``begin`` 和 ``end`` 是必须的）:

        =================   ====================================================
        ``name``            包含标记的作用域名称。
        ``contentName``     排除标记的内容区域作用域名称。
        ``begin``           起始标记模式。
        ``end``             结束标记模式。
        ``name``            整个区域的作用域名称。
        ``beginCaptures``   ``begin`` 的 ``captures`` 。 参考 ``captures`` 。
        ``endCaptures``     ``end`` 的 ``captures`` 。 参考 ``captures`` 。
        ``patterns``        用于匹配内容区域的模式列表。
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

库
**********

可以在 ``patterns`` 中引用，或者使用 ``include`` 元素来引用自身。
参看 ``include`` 以获取更多信息。

库中可以起包含如下元素:

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


转义序列
****************

请根据需要对 JSON/XML 字符串进行转义。

.. EXPLAIN

对于 YAML ， 需要额外注意不要遗漏了字符串的引号，从而导致意外的开始了一个新的分量。 by not using quotes for your strings. 下面是一些 **不能** 正常工作的例子::

    match: [aeiou]

    include: #this-is-actually-a-comment

    match: "#"\w+""
