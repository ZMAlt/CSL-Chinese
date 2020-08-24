[TOC]



# CSL 1.0.1 Specification

## 介绍

CSL 是一种基于 XML 的格式，用来描述引用的格式，注释和参考文献，提供了

- 一种开放的(开源的)格式

- 紧凑并且稳定的格式

- 对格式要求的外部支持

- 格式的发布和更新的基本支持

- 数千种免费提供 的样式

  

有关的其他文档，CSL 介绍，样式和 locale 详见 [CSL 项目主页](https://citationstyles.org/)

### 术语

关键字 `MUST,MUST NOT,REQUIRED,SHALL,SHALL NOT,SHOULD,SHOULD NOT,RECOMMENDED,MAY 和 OPTIONAL` 将按[IETF RFC 2119](http://tools.ietf.org/html/rfc2119)中的描述解释。

## 命名习惯

在引用 CSL 元素时，将在前面使用`cs:`。

## 文件类型

这里有3种CSL文件类型：从属样式和独立样式 (都使用`.csl`作为扩展名)，以及 locale files (名字为`"locales-xx-XX.xml"`，其中 `"xx-XX"`表示语言以及其方言，e.g. `"en-US"` 表示美式英语)。

### 独立格式

独立格式包含引文，笔记以及参考文献的格式指示。虽然它们大多数自包含的，但是依赖于 locale files。

### 从属格式

从属样式是独立样式的别名，其内容仅包含样式元数据，不包括任何格式说明。通过将具有相同引用风格的期刊（e.g., “Nature Biotechnology”, “Nature Nanotechnology”）的从属样式链接到独立样式（e.g., “Nature Journals”），从属样式就不再需要重复的格式说明。

### Locale Files

每个 Locale file 包含一系列对某种语言/方言的本地化数据（词语翻译，本地化日期格式以及语法选项）

## XML 声明

每个样式或者 locale 应该以 XML 声明开头，给出具体的 XML 版本以及字符编码。大多数情况下，XML 声明是：

```xml
<?xml version="1.0" encoding="UTF-8"?>
```

## 引用格式结构(Styles)

### 根元素 `cs:style`

样式的根元素是`cs:style`。在独立格式种，根元素携带以下几种属性：

`class`

​	决定样式的引文类型是 in-text (值是 `"in-text"`) 或者 note (值是`"note"`) 。

`default-locale` (可选的)

​	为本地化设值默认的 locale。值必须是 [locale code](http://books.xmlschemata.org/relaxng/ch19-77191.html)。

`version`

​	样式的 CSL 版本。对于 CSL 1.0 兼容样式，必须是 `"1.0"`。

此外，`cs:style`可能携带任意的 [全局选项](https://docs.citationstyles.org/en/stable/specification.html#global-options) 和 [可继承名称选项](https://docs.citationstyles.org/en/stable/specification.html#inheritable-name-options)。

在这些属性种，在从属格式中，只有`version`是必须的，默认的 locale 属性可以设置为代替的 locale。其他的属性是可以忽略的。

下面是一个独立样式的 `cs:style`示例，前面是 XML 声明：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<style xmlns="http://purl.org/net/xbiblio/csl" version="1.0" class="in-text" default-locale="fr-FR"/>
```

### ` cs:style`的子元素

在独立格式中，`cs:style`有以下子元素：

`cs:info`

​	`info`元素必须是`cs:style`的第一个子元素。其中包括了用来描述样式的元数据 (style name, ID, authors, etc.).

`cs:citation`

​	必要元素，用来描述 in-text 引文或者 notes 的格式。

`cs:bibliography` (可选)

​	可能会出现一次。描述参考文献的格式。

`cs:macro` (可选)

​	可能多次出现。宏通过格式化指示来重复使用，可以使样式更加的紧凑和易维护。

`cs:locale` (可选)

​	可能多次出现。用于指定或者覆盖本地化数据。



在从属格式中，`cs:style`只有一个子元素，`cs:info`。

#### Info

`cs:info`包含了样式的元数据。其结构基于[Atom Syndication Format](http://tools.ietf.org/html/rfc4287)。在独立格式中，`cs:info`有下面的几个子元素：



`cs:author`和`cs:contributor` （可选）

​	`cs:author`和`cs:contributor`分别用来致谢样式的作者和贡献者，可能被使用多次。在这些元素中，子元素`cs:name`必须出现一次，`cs:email`和`cs:uri`则可能出现一次。这些子元素分别代表作者或者贡献者的名字，邮箱和URI。

`cs:category` (可选)

​	样式可能被分类到一个或者多个类别，`cs:category`可能被使用一次，用来描述 in-text 引文怎么渲染。使用`citation-format`属性设置其为以下几种情形：

- “author-date” - e.g. “… (Doe, 1999)”

- “author” - e.g. “… (Doe)”

- “numeric” - e.g. “… [1]”

- “label” - e.g. “… [doe99]”

- “note” - the citation appears as a footnote or endnote

  `cs:categroy`也可能在携带`field`属性时多次使用，用来对学科进行分类（见附录I）。

`cs:id`

​	必须出现一次。该元素应该包含一个URI以建立样式的`ID`，对于公开可用的样式，需要一个稳定、唯一的并可以引用的 URI。

`cs:issn/cs:essn/cs:issnl`（可选）

​	`cs:issn`元素可以多次使用，用来表示该 CSL 对应的期刊的 ISSN 。 `cs:eissn` 和`cs:issnl` 可以分别用来表示 eISSN and [ISSN-L](http://www.issn.org/2-22637-What-is-an-ISSN-L.php) 。

`cs:link` (可选)

​	可以使用多次。`cs:link`必须携带两个属性`href`和`rel`。`href`用来设置 URI （通常情况下为 URL），`rel`表明 URI 与当前样式的关系，它的值可能是：

- “self” - 样式的 URI
- “template” - 用来编写该样式的模板的 URI
- “documentation - 该样式的文档

`cs:published` (可选)

​	`cs:published`必须是一个[时间戳](http://books.xmlschemata.org/relaxng/ch19-77049.html)，用来表明样式创建的时间或者可获得的时间。

`cs:rights` (可选)

​	`cs:rights`表明了该 CSL 的 license，可能会携带`license`属性。

`cs:summary` (可选)

​	给出该 CSL 的简单描述。

`cs:title` 

​	其内容应该是该 CSL 展示给使用者的名字。

`cs:title-short` (可选)

​	是上述名字的缩写，比如`APA`

`cs:updated` 

​	内容是一个[时间戳](http://books.xmlschemata.org/relaxng/ch19-77049.html)，用来表示该 CSL 的最后更新时间。



`cs:link`, `cs:rights`, `cs:summary`, `cs:title` 和 `cs:title-short`元素可以携带`xml:lang`属性用来表示元素内容的语言（值必须是[xsd:language locale code](http://books.xmlschemata.org/relaxng/ch19-77191.html)）。对于`cs:link`，该属性可以用来表示链接目标的语言。

在从属格式中，当`cs:link`中的 `href`为其父格式的 URI时，`rel`属性必须设置为`"independent-parent"`。此外，从属格式的`ref`不能设置为`"template"`，这在独立格式中才能使用。



下面是一个独立样式`cs:info`的例子：

```xml
<info>
  <title>Style Title</title>
  <id>http://www.zotero.org/styles/style-title</id>
  <link href="http://www.zotero.org/styles/style-title" rel="self"/>
  <author>
    <name>Author Name</name>
    <email>name@domain.com</email>
    <uri>http://www.domain.com/name</uri>
  </author>
  <category citation-format="author-date"/>
  <category field="zoology"/>
  <updated>2008-10-29T21:01:24+00:00</updated>
  <rights license="http://creativecommons.org/licenses/by-sa/3.0/">This work
  is licensed under a Creative Commons Attribution-Share Alike 3.0 Unported
  License</rights>
</info>
```

#### Citation

`cs:citation`元素描述了引文的格式，其中引文可以是一条或者多条。引文的格式可能是in-text citations (`author (e.g. “[Doe]”)`, `author-date (“[Doe 1999]”)`, `label (“[doe99]”`) 或者 `number (“[1]”)` ) 或者 notes。这要求`cs:layout`子元素来描述什么样的数据，以及怎么被引用（见`cs:layout`）。在`cs:layout`之前可能会有`cs:sort`元素，用来描述引文的排序（见排序）。`cs:citation`元素可能携带一些属性。下面是一个`cs:citation`的例子：

```xml
<citation>
  <sort>
    <key variable="citation-number"/>
  </sort>
  <layout>
    <text variable="citation-number"/>
  </layout>
</citation>
```

**A note to CSL processor developers**

#### Bioliography

`cs:bioliography`元素描述了参考文献条目的格式。同`cs:citation`相同，其子元素`cs:layout`用来描述每个条目的格式，`cs:sort`元素用来描述条目的排序。下面是一个`cs:bioliography`的例子：

```xml
<bibliography>
  <sort>
    <key macro="author"/>
  </sort>
  <layout>
    <group delimiter=". ">
      <text macro="author"/>
      <text variable="title"/>
    </group>
  </layout>
</bibliography>
```

#### Macro

宏，使用`cs:macro`元素定义，包含了格式的指令。宏可以在其他宏，`cs:layout`元素(`cs:citation`和`cs:bioliography`中)，`cs:key`元素(`cs:sort`)中通过`cs:text`调用。宏在文件中位置的通常建议是：放在`cs:locale`元素后以及`cs:citation`元素前。

宏通过`cs:macro`元素的属性`name`的值来调用。`cs:macro`必须啊包含一个或者多个渲染元素。

使用宏可以提高样式的可读性，紧凑性以及可维护性。建议用过宏调用来保持`cs:citation`元素和`cs:bioliography`元素的内容紧凑。为了再其他样式中方便的重复使用，宏名字建议使用通用的名字。

下面是一个实例：引文中包括项目题目，并当条目为"book"时，设置字体为`italic`。

```xml
<style>
  <macro name="title">
    <choose>
      <if type="book">
        <text variable="title" font-style="italic"/>
      </if>
      <else>
        <text variable="title"/>
      </else>
    </choose>
  </macro>
  <citation>
    <layout>
      <text macro="title"/>
    </layout>
  </citation>
</style>
```

#### Locale

来自`"locales-xx-XX.xml"`locale file 的本地化数据可以通过`cs:locale`元素来重定义或者补充定义。`cs:locale`元素应该放在`cs:info`元素后。

`cs:locale`元素的`xml:lang`属性是可选的，必须设置为[xsd:language locale code](http://books.xmlschemata.org/relaxng/ch19-77191.html)中的一种，用来确定使用的语言环境（或方言，见 locale fallback）。

See [Terms](https://docs.citationstyles.org/en/stable/specification.html#terms), [Localized Date Formats](https://docs.citationstyles.org/en/stable/specification.html#localized-date-formats) and [Localized Options](https://docs.citationstyles.org/en/stable/specification.html#localized-options) for further details on the use of `cs:locale`.

下面是一个`cs:locale`元素的例子：

```xml
<style>
  <locale xml:lang="en">
    <terms>
      <term name="editortranslator" form="short">
        <single>ed. &amp; trans.</single>
        <multiple>eds. &amp; trans.</multiple>
      </term>
    </terms>
  </locale>
</style>
```

**Locale Fallback**

Locale file 为语言方言提供了本地化数据；可选的`cs:locale`元素的`xml:lang`属性设置为一种语言(e.g. “en” for English) 或者方言，`xml:lang`属性也可能缺失。Localr fallback 是一种在上述的属性设置中检索来确定本地化单元的机制。这些本地化单元包括日期格式，本地化选项或者术语的特定形式。

对于同一种语言的方言，一种被称为初级方言，其他都是二级方言。下面展示了部分语言的初级方言和二级方言：

| 初级方言 | 二级方言     |
| -------- | ------------ |
| de-DE    | de-AT, de-CH |
| en-US    | en-GB        |
| pt-PT    | pt-BR        |
| zh-CN    | zh-TW        |

这里用一个例子来描述 Locale fallback。如果要选择 `"de-AT"`(Austrian German)，本地化单元可以来自下面的源（优先级逐渐降低）：# 这里有点问题

A. `cs:locale`元素

- `xml:lang`设置为方言 `"de-AT"`
- `xml:lang`设置为 `"de"`
- `xml:lang`不设置

B. Locale files

- `xml:lang`设置为方言  `"de-AT"`
- `xml:lang`设置为对应的初级方言 `"de-DE"` (standard german)
- `xml:lang`设置为`"en-US"`

也就是说，如果要使用`"de-AT"`语言，首先在 Locale files 中寻找`"de"`对应的 files，即`"de-AT"`和`"de-DE"`，由于使用的是方言`"de-AT"`，所以选择`de-AT`对应的 locale file。接下来，如果 csl 文件中包含 `cs:locale`元素，将会覆盖 locale file 文件的设置。

## Locale Files

尽管本地化数据可以包括在 csl 文件中(见[Locale](#Locale))，但是 Locale file 可以方便的提供本地化数据的设置，包括术语，日期格式以及语法选项。

每个 Locale file 包括了一种语言方言的本地化数据。locale file 中根元素为`cs:locale`。在`cs:locale`根元素中，属性`xml:lang`用来设置方言。同时这一设置选项也用来对 locale file w文件命名（`"xx-XX"`文件名为`locales-xx-XX.xml`）。此外，根元素必须携带`version`属性，表明 locale file 的 CSL 版本（对 CSL 1.0 兼容的 locale file 必须设置为`"1.0"`）。Locale file 有和样式同样的[命名空间](https://docs.citationstyles.org/en/stable/specification.html#namespacing) 。`cs:locale`元素可能包含`cs:info`作为第一个子元素，同时，必须含有`cs:terms`、`cs:date`、`cs:style-options`子元素。下面是一个 locale file 的部分示例：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<locale xml:lang="en-US" version="1.0" xmlns="http://purl.org/net/xbiblio/csl">
  <style-options punctuation-in-quote="true"/>
  <date form="text">
    <date-part name="month" suffix=" "/>
    <date-part name="day" suffix=", "/>
    <date-part name="year"/>
  </date>
  <date form="numeric">
    <date-part name="year"/>
    <date-part name="month" form="numeric" prefix="-" range-delimiter="/"/>
    <date-part name="day" prefix="-" range-delimiter="/"/>
  </date>
  <terms>
    <term name="no date">n.d.</term>
    <term name="et-al">et al.</term>
    <term name="page">
      <single>page</single>
      <multiple>pages</multiple>
    </term>
    <term name="page" form="short">
      <single>p.</single>
      <multiple>pp.</multiple>
    </term>
  </terms>
</locale>
```

### Info

`cs:info`元素用来给出 locale file 的元数据。它含有以下子元素：

`cs:translator`(可选)

​	`cs:translator`用来致谢翻译者，并且能使用多次。在这个元素中。子元素`cs:name`必须出现一次，`cs:uri`则是可选的。这些子元素应该分别包括翻译者的名字，地址和 URI。

`cs:rights`(可选)

​	可能出现一次。`cs:rights`的内容用来指定 locale file 发布版本的 license。该元素可能会携带`license`属性来指定`license`的 URI，`xml:lang`属性则用来指定元素内容的语言（值必须是[xsd:language locale code](http://books.xmlschemata.org/relaxng/ch19-77191.html)）。

`cs:updates`(可选)

​	`cs:updated`元素的内容必须是一个时间戳来指定 locale file 最后一次更新的时间。

### Terms(术语)

术语是本地化的字符串（比如通过使用`"and"`术语，`"Doe and Smith"`在 locale 从英语到德语的转换中会自动变为`"Doe und Smith"`）。术语用`cs:term`元素定义，是`cs:terms`元素的子元素。每个`cs:term`元素必须携带一个`name`属性，设置为[附录II 术语](#附录II 术语)。

术语可以直接在`cs:term`的内容中定义，或者，in cases where singular and plural variants are needed (e.g. “page” and “pages”), in the content of the child elements `cs:single` and `cs:multiple`, respectively。

Terms may be defined for specific forms by using `cs:term` with the optional `form` attribute set to:

- “long” - (default), e.g. “editor” and “editors” for the “editor” term
- “short” - e.g. “ed.” and “eds.” for the term “editor”
- “verb” - e.g. “edited by” for the term “editor”
- “verb-short” - e.g. “ed.” for the term “editor”
- “symbol” - e.g. “§” and “§§” for the term “section”

#### 序数后缀

#### 特定序数

### 本地化日期格式

在`cs:date`元素中，本地化数据格式有两种格式：一种是`"numeric"`（例：`12-15-2005`），另外一种是 `"text"`（例：`December 15,2005`）。格式在`cs:date`元素中，使用`form`属性来设置。

日期格式使用`cs:date-part`子元素来构建（见[Date-part](https://docs.citationstyles.org/en/stable/specification.html#date-part)）。With a required `name` attribute set to either `day`, `month` or `year`, the order of these elements reflects the display order of respectively the day, month, and year. 日期可以使用`cs:date`和`cs:date-part`元素中的[formatting](https://docs.citationstyles.org/en/stable/specification.html#formatting) 和 [text-case](https://docs.citationstyles.org/en/stable/specification.html#text-case)属性设置。`cs:date`元素中的`delimiter`属性用来设置`cs:date-part`中不同部分的间隔and [affixes](https://docs.citationstyles.org/en/stable/specification.html#affixes) may be applied to the `cs:date-part` elements.

**Note** Affixes are not allowed on `cs:date` when defining localized date formats. This restriction is in place to separate locale-specific affixes (set on the `cs:date-part` elements) from any style-specific affixes (set on the calling `cs:date` element), such as parentheses. An example of a macro calling a localized date format:

```xml
<macro name="issued">
 <date variable="issued" form="numeric" prefix="(" suffix=")"/>
</macro>
```

### 本地化选项

本地化选项有两个，`limit-day-ordinals-to-day-1` 和 `punctuation-in-quote` （见[Locale Options](https://docs.citationstyles.org/en/stable/specification.html#locale-options)）。这些全局选项（同时影响引文和参考文献条目）在`cs:style-options`中被设置为可选属性。

## 渲染元素

渲染元素指定了在引文或参考文献条目中需要包含哪些条目元数据，以及以何种方式排序，并对其格式进行控制。

### Layout

`cs:layout`渲染元素是`cs:citation`元素和`cs:bibliography`元素的的必要子元素。`cs:layout`必须包含一个或者多个渲染元素，并且可能携带`affixes`和格式化属性。在`cs:citation`元素中，`delimiter`属性用来指定一个引文中的不同引用的间隔符。例如：一个`"(1,2)"`类型的引文可以使用下面的代码实现：

```xml
<citation>
  <layout prefix="(" suffix=")" delimiter=", ">
    <text variable="citation-number"/>
  </layout>
</citation>
```

### Text

`cs:text`渲染元素用来输出文字。其必须携带下面的属性来确定什么部分需要渲染：

- `variable`- 渲染一个变量的文本内容。属性值必须是[标准变量](#标准变量)。可以与`form`属性一起选择变量的“long”（默认）或“short”形式（e.g. the full or short title）。如果选择了`"short"`形式，但是却没法获得，就使用`"long"`形式来渲染。
- `macro` - 渲染宏的文字输出。属性值必须和`cs:macro`元素的`name`属性的值相匹配。
- `term` - 渲染术语。属性必须是[Appendix II-Terms](Appendix II-Terms)中的术语列表中的一个。May be accompanied by the `plural` attribute to select the singular (“false”, default) or plural (“true”) variant of a term, and by the `form` attribute to select the “long” (default), “short”, “verb”, “verb-short” or “symbol” form variant (see also [Terms](https://docs.citationstyles.org/en/stable/specification.html#terms)).
- `value` - 渲染属性值自己。

一个`cs:text`的渲染`"title"`变量的例子：

```xml
<text variable="title"/>
```

`cs:text` 可能会携带 [affixes](https://docs.citationstyles.org/en/stable/specification.html#affixes), [display](https://docs.citationstyles.org/en/stable/specification.html#display), [formatting](https://docs.citationstyles.org/en/stable/specification.html#formatting), [quotes](https://docs.citationstyles.org/en/stable/specification.html#quotes), [strip-periods](https://docs.citationstyles.org/en/stable/specification.html#strip-periods) 和 [text-case](https://docs.citationstyles.org/en/stable/specification.html#text-case) 属性。

### Date

`cs:date`渲染元素输出从[日期变量](#日期变量)列表中的选择的日期。日期可以以本地化或者非本地化格式呈现。

本地化的日期格式通过可选的`form`属性来选择，其值必须设置为`"numeric"`(for fully numeric formats, e.g. “12-15-2005”)或者`"text"`(for formats with a non-numeric month, e.g. “December 15, 2005”)。本地化日期格式可以通过两种方式进行自定义。第一种：`date-parts`属性可以用来设置较少的日期部分。可能得值为：

- “year-month-day” - 默认值，渲染年，月，日
- “year-month” - 渲染年月
- “year” - 只渲染年

第二种，`cs:date`可以含有一个或者多个`cs:date-part`子元素。在这些子元素种可以设置属性来覆盖之前的设置(e.g. to get abbreviated months for all locales, the `form` attribute on the month-`cs:date-part` element can be set to “short”)。这些子元素不影响各个日期部分渲染的顺序和以及其是否渲染。 [Affixes](https://docs.citationstyles.org/en/stable/specification.html#affixes), which are very locale-specific, are not allowed on these `cs:date-part` elements.

没有属性`form`的情况下,`cs:date`则描述了一个自带的非本地化的日期格式。其日期格式使用`cs:date-part`子元素来构建。在使用`name`属性并设置为`day`,`month`或`year`时，这些元素的顺序反应了其显示顺序。日期可以在`cs:date-part`元素种使用`formatting`属性以及多个`cs:date-part`的属性来格式化。`cs:date`种的`delimiter`属性可以用来设置`cs:date-part`元素不同日期部分的分隔符，and [affixes](https://docs.citationstyles.org/en/stable/specification.html#affixes) may be applied to the `cs:date-part` elements。

本地化的日期或者是非本地化的日期，, `cs:date` 都可能携带 [affixes](https://docs.citationstyles.org/en/stable/specification.html#affixes), [display](https://docs.citationstyles.org/en/stable/specification.html#display), [formatting](https://docs.citationstyles.org/en/stable/specification.html#formatting) 和[text-case](https://docs.citationstyles.org/en/stable/specification.html#text-case) 属性。

#### Date-part

`cs:date-part`元素用来控制日期的各个部分怎么渲染。除了其父元素`cs:date`调用了本地化日期格式，这些子元素同样可以决定哪些部分出现以及各部分的渲染顺序。`cs:date-part`元素描述了`name`属性选择的日期部分，其`name`值可以是：

**“day”**

​	对于`"day"`来说，`cs:date-part`可能会携带`form`属性，值可以设置为：

- “numeric” - (default), e.g. “1”

- “numeric-leading-zeros” - e.g. “01”

- “ordinal” - e.g. “1st”

  有的语言种，比如法语，只在月份的第一天使用`"oridinal"`也就是序数形式(“1er janvier”, “2 janvier”, “3 janvier”, etc.)。这种输出可以通过`"oridinal"`以及`limit-day-oridinals-to-day`属性来设置 (see [Locale Options](https://docs.citationstyles.org/en/stable/specification.html#locale-options))。

**“month”**

​	对于`"month"`来说，`cs:date-part`可能会携带`strip-periods`和`form`属性。在locale files中，月份缩写应该后面要加点（e.g. “Jan.”, “Feb.”）。点可以设置`strip-periods`为`"true"`去掉。`form`属性可以设置为：

- “long” - (default), e.g. “January”
- “short” - e.g. “Jan.”
- “numeric” - e.g. “1”
- “numeric-leading-zeros” - e.g. “01”

**“year”**

​	对`"year"`来说，`cs:date-part`可能会携带`form`属性，值可以设置为：

- “long” - (default), e.g. “2005”
- “short” - e.g. “05”

`cs:date-part` 也可能携带[formatting](https://docs.citationstyles.org/en/stable/specification.html#formatting), [text-case](https://docs.citationstyles.org/en/stable/specification.html#text-case) 和 `range-delimiter` 属性。 Attributes for [affixes](https://docs.citationstyles.org/en/stable/specification.html#affixes) are allowed, unless `cs:date` calls a localized date format.

#### Date Ranges

默认的日期范围中的分隔符是短线(en-dash)，比如：“May–July 2008”。可以在`cs:date-part`元素中通过`range-delimiter`属性来设置常用的分隔符。当日期范围被渲染的时候，范围分隔符从`cs:date-part`元素中提取，并且匹配两个日期差别中最大的部分(“year”, “month”, 或 “day”)。如下面的例子，将会渲染出类似`“1-4 May 2008”, “May–July 2008” and “May 2008/June 2009”`的日期范围。

```xml
<style>
  <citation>
    <layout>
      <date variable="issued">
        <date-part name="day" suffix=" " range-delimiter="-"/>
        <date-part name="month" suffix=" "/>
        <date-part name="year" range-delimiter="/"/>
      </date>
    </layout>
  </citation>
</style>
```

#### AD and BC

`“ad”`一词（Anno Domini）自动附加到小于四位数的正年份（例如，`“79”`变为`“79AD”`）。`“bc”`一词（Before Christ）自动附加到负年份（例如，`“-2500”`变为`“2500BC”`）。

#### Seasons

如果日期中包含了季节而不是月份，日期术语(`“season-01” `到` “season-04”`, 分别代表春夏秋冬)将取代月份术语。比如，下面将会被渲染为`“May 2008”` 和` “Winter 2009”`

```xml
<style>
  <citation>
    <layout>
      <date variable="issued">
        <date-part name="month" suffix=" "/>
        <date-part name="year"/>
      </date>
    </layout>
  </citation>
</style>
```

#### Approximate Dates

Approximate dates test “true” for the `is-uncertain-date` conditional (see [Choose](https://docs.citationstyles.org/en/stable/specification.html#choose)). For example,would result in “2005” (normal date) and “ca. 2003” (approximate date).

```xml
<style>
  <citation>
    <layout>
      <choose>
        <if is-uncertain-date="issued">
          <text term="circa" form="short" suffix=" "/>
        </if>
      </choose>
      <date variable="issued">
        <date-part name="year"/>
      </date>
    </layout>
  </citation>
</style>
```

### Number

`cs:number`渲染元素输出`variable`属性选择的数字变量。数字变量是标准变量的子集（见附录 变量）。

使用`cs:number`元素来渲染数字变量时，如果只包含数字内容(as determined by the rules for `is-numeric`, see [Choose](https://docs.citationstyles.org/en/stable/specification.html#choose))，数字就被提取出来。变量内容包含非数字内容时，变量内容将呈现为原样。

在提取的过程中，用连字符分隔的数字将去掉中见的空格（“2 - 4” 变为 “2-4”）。用逗号分隔的数字在都好后会添加一个空格，并删掉其余的空格(“2,3” 和 “2 , 3” 变为 “2, 3”)。当数字使用`&`分隔时，在前后各添加一个空格(“2&3” 变为 “2 & 3”)。

提取的数字可以通过`form`属性行进格式化，其值可以设置为：

- “numeric” - 默认, e.g. “1”, “2”, “3”
- “ordinal” -  序数数字 e.g. “1st”, “2nd”, “3rd”。序数后缀可以使用术语定义 (see [Ordinal Suffixes](https://docs.citationstyles.org/en/stable/specification.html#ordinal-suffixes)).
- “long-ordinal” - 长序数 e.g. “first”, “second”, “third”。Long ordinals are defined with the [terms](https://docs.citationstyles.org/en/stable/specification.html#terms) “long-ordinal-01” to “long-ordinal-10”, which are used for the numbers 1 through 10. For other numbers “long-ordinal” falls back to “ordinal”.
- “roman” - 罗马数字 e.g. “i”, “ii”, “iii”

带有前缀或者后缀的数字不能使用罗马数字进行排序或者渲染(e.g. “2E” 仍然时 “2E”)。没有词缀的数字可以被分别转换(“2, 3” 可以转换为 “2nd, 3rd”, “second, third” 或者 “ii, iii”)。

`cs:number`元素可能会携带[affixes](https://docs.citationstyles.org/en/stable/specification.html#affixes), [display](https://docs.citationstyles.org/en/stable/specification.html#display), [formatting](https://docs.citationstyles.org/en/stable/specification.html#formatting) 和 [text-case](https://docs.citationstyles.org/en/stable/specification.html#text-case) 属性。

### Names

`cs:names`y元素用来输出一个或多个名字变量的内容（通过必选属性`variable`来选择），。

```xml
<names variable="editor translator" delimiter="; ">
  <label prefix=" (" suffix=")"/>
</names>
```

`cs:names`有四个子元素:`cs:name`,`cs:et-al`,`cs:substitute`,`cs:label`。 `cs:names` element 元素可能会携带 [affixes](https://docs.citationstyles.org/en/stable/specification.html#affixes), [display](https://docs.citationstyles.org/en/stable/specification.html#display) 和 [formatting](https://docs.citationstyles.org/en/stable/specification.html#formatting) 属性。如果选择了多个变量，每个变量将按照顺序独立的渲染，只有当选择的出现"editor"和"translator"时，并且两个是相同时，只渲染一个内容。此外，如果`cs:names`元素包含`cs:label`元素，则使用“editortranslator”术语，替换默认的“editor”和“translator”术语。`cs:names`元素中的`delimiter`属性可以用来设置不同名字变量的名字。

#### Name

`cs:name`元素的一个可选的子元素`cs:names`可以用来描述单个`names`的格式，以及名称变量中不同`names`的分隔。`cs:name`可能携带下面的属性：

`and`

​	在名称变量中用来设置倒数第二个名字和倒数第一个名字的分隔符。可选的值为`"text"`(“Doe, Johnson and Smith”)或者`"symbol"`(eg: “Doe, Johnson & Smith”)。

`delimiter`

​	在名称变量中设置字符串来分隔名称。默认的是`","`(e.g. “Doe, Smith”)。

`delimiter-precedes-et-al`

​	确定何时使用分隔符分隔名称变量中的倒数第一个作者和最后一个作者。（如果`and`没有被设置，则使用`delimiter`，不管`delimiter-precedes-et-al`的值）。该属性可选的值有：

- “contextual” - 默认，在作者列表中有3个或者3个以上的人时使用。
  - 2 个名字: “J. Doe and T. Williams”
  - 3 个名字: “J. Doe, S. Smith, and T. Williams”
- “after-inverted-name” -仅前面的名称因`name-as-sort-order` 属性颠倒时，才使用分隔符。E.g. `name-as-sort-order`设置为`"first"`
  - “Doe, J., and T. Williams”
  - “Doe, J., S. Smith and T. Williams”
- “always” - 分隔符一直使用。
  - 2 names: “J. Doe, and T. Williams”
  - 3 names: “J. Doe, S. Smith, and T. Williams”
- “never” - 禁用分隔符
  - 2 names: “J. Doe and T. Williams”
  - 3 names: “J. Doe, S. Smith and T. Williams”



#### Et-al

Et-al缩写通过`et-al-...`属性来控制，同时也可以使用可选的`cs:et-al`元素设置，`cs:et-al`元素必须放在`cs:name`元素后。`term`属性可以被设置为`"et-al"`（默认）或者`"and others"`。下面是 `"et-al"`术语的一个例子：

```xml
<names variable="author">
  <et-al term="and others" font-style="italic"/>
</names>
```

#### Substitute

可选的`cs:substitute`元素，是`cs:names`的子元素，且必须是最后一个子元素，在父元素`cs:names`中指定的名称变量为空时添加替换。替换必须放在`cs:substitute`元素种，并且必须包含一个或者多个渲染元素（除`cs:layout`）。`cs:names`的简洁版本没有子元素，继承了`cs:names`元素中在`cs:name`和`cs:et-al`子元素的属性值。如果`cs:substitute`元素包含了多个子元素，第一个非空的元素用于替换。替换变量在输出的其余部分被抑制，以防止重复。下面的例子中：`"author"`名称变量为空时，就被`"editor"`名称变量替换，在没有 editor 时，则使用`"title"`宏替换。

```xml
<macro name="author">
  <names variable="author">
    <substitute>
      <names variable="editor"/>
      <text macro="title"/>
    </substitute>
  </names>
</macro>
```



#### Label in `cs:names`

`cs:label`元素时可选的，而且必须位于`cs:name`和`cs:et-al`元素后，在`cs:substitute`元素前。当`cs:label`作为`cs:names`元素的子元素时，`cs:label`不能携带`variable`属性，而是使用父元素`cs:names`中的变量。A second difference is that the `form` attribute may also be set to “verb” or “verb-short”, so that the allowed values are:

- “long” - (default), e.g. “editor” and “editors” for the “editor” term
- “short” - e.g. “ed.” and “eds.” for the term “editor”
- “verb” - e.g. “edited by” for the term “editor”
- “verb-short” - e.g. “ed.” for the term “editor”
- “symbol” - e.g. “§” and “§§” for the term “section”

### Label

`cs:label`渲染元素输出与所选变量匹配的术语，该属性必须设置为“locator”、“page”或数字变量之一。只有当选择的变量是非空的受，术语才会渲染。例如：

```xml
<group delimiter=" ">
  <label variable="page"/>
  <text variable="page"/>
</group>
```

可以生成`"page 3"`或者`"pages 5-7"`。`cs:label`可能会携带下面的属性:

`form`

​	选择术语的形式，可以使用下面的值：

- “long” - (default), e.g. “page”/”pages” for the “page” term
- “short” - e.g. “p.”/”pp.” for the “page” term
- “symbol” - e.g. “§”/”§§” for the “section” term

`plural`

​	设置术语的复数形式，可以使用下面的值：

- “contextual” - (default), the term plurality matches that of the variable content. Content is considered plural when it contains multiple numbers (e.g. “page 1”, “pages 1-3”, “volume 2”, “volumes 2 & 4”), or, in the case of the “number-of-pages” and “number-of-volumes” variables, when the number is higher than 1 (“1 volume” and “3 volumes”).
- “always” - always use the plural form, e.g. “pages 1” and “pages 1-3”
- “never” - always use the singular form, e.g. “page 1” and “page 1-3”

`cs:label`也可能会携带[affixes](https://docs.citationstyles.org/en/stable/specification.html#affixes), [formatting](https://docs.citationstyles.org/en/stable/specification.html#formatting), [text-case](https://docs.citationstyles.org/en/stable/specification.html#text-case) 和 [strip-periods](https://docs.citationstyles.org/en/stable/specification.html#strip-periods) 属性。

### Group

`cs:group`元素必须包含一个或者多个渲染元素（除了 `cs:layout`）。`cs:group`可以携带`delimiter`属性来分隔子元素以及`affixes`、`display`和`formatting`属性。`cs:group`隐含的条件，当以下情况出现时，`cs:group`和它的子元素将会被抑制：a)在`cs:group`中至少一个渲染元素调用了变量（直接地或者通过宏调用），b)所有被调用得变量都为空。

```xml
<layout>
  <group delimiter=" ">
    <text term="retrieved"/>
    <text term="from"/>
    <text variable="URL"/>
  </group>
</layout>
```

上述代码可以生成`“retrieved from http://dx.doi.org/10.1128/AEM.02591-07”`类似得结果，但是当URL为空的时候，不生成结果。

### Choose

`cs:choose`元素允许有条件的渲染元素。下面的例子的意思为：在`"issud"`日期变量存在的情况下，就实施渲染，如果不存在，就给出`"no date"`术语：

```xml
<choose>
  <if variable="issued">
    <date variable="issued" form="numeric"/>
  </if>
  <else>
    <text term="no date"/>
  </else>
</choose>
```

`cs:choose`元素中必须还有`cs:if`子元素，后面还可以有一个或者多个`cs:else-if`子元素以及一个可选的用来结尾的`cs:else`元素。`cs:if`和`cs:else-if`元素可能回包含任意个除`cs:layout`的渲染元素。由于空的`cs:else`元素是多余的，所以`cs:else`元素必须至少包含一个渲染元素。`cs:if`和`cs:else-if`元素必须含有一个或多个判断条件，这些条件可以使用下买的属性设置：

`disambiguate`

​	当改属性设置为`"true"`(并且是唯一允许的值)的时候，When set to “true” (the only allowed value), the element content is only rendered if it disambiguates two otherwise identical citations. This attempt at [disambiguation](https://docs.citationstyles.org/en/stable/specification.html#disambiguation) is only made when all other disambiguation methods have failed to uniquely identify the target source.

```
is-numeric
```

Tests whether the given variables ([Appendix IV - Variables](https://docs.citationstyles.org/en/stable/specification.html#appendix-iv-variables)) contain numeric content. Content is considered numeric if it solely consists of numbers. Numbers may have prefixes and suffixes (“D2”, “2b”, “L2d”), and may be separated by a comma, hyphen, or ampersand, with or without spaces (“2, 3”, “2-4”, “2 & 4”). For example, “2nd” tests “true” whereas “second” and “2nd edition” test “false”.

```
is-uncertain-date
```

Tests whether the given [date variables](https://docs.citationstyles.org/en/stable/specification.html#date-variables) contain [approximate dates](https://docs.citationstyles.org/en/stable/specification.html#approximate-dates).

```
locator
```

Tests whether the locator matches the given locator types (see [Locators](https://docs.citationstyles.org/en/stable/specification.html#locators)). Use “sub-verbo” to test for the “sub verbo” locator type.

```
position
```

Tests whether the cite position matches the given positions (terminology: citations consist of one or more cites to individual items). When called within the scope of cs:bibliography, `position` tests “false”. The positions that can be tested are:

- “first”: position of cites that are the first to reference an item

- “ibid”/”ibid-with-locator”/”subsequent”: cites referencing previously cited items have the “subsequent” position. Such cites may also have the “ibid” or “ibid-with-locator” position when:

  1. the current cite immediately follows on another cite, within the same citation, that references the same item

  or

  1. the current cite is the first cite in the citation, and the previous citation consists of a single cite referencing the same item

  If either requirement is met, the presence of locators determines which position is assigned:

  - **Preceding cite does not have a locator**: if the current cite has a locator, the position of the current cite is “ibid-with-locator”. Otherwise the position is “ibid”.
  - **Preceding cite does have a locator**: if the current cite has the same locator, the position of the current cite is “ibid”. If the locator differs the position is “ibid-with-locator”. If the current cite lacks a locator its only position is “subsequent”.

- “near-note”: position of a cite following another cite referencing the same item. Both cites have to be located in foot or endnotes, and the distance between both cites may not exceed the maximum distance (measured in number of foot or endnotes) set with the `near-note-distance` option (see [Note Distance](https://docs.citationstyles.org/en/stable/specification.html#note-distance)).

Whenever position=”ibid-with-locator” tests true, position=”ibid” also tests true. And whenever position=”ibid” or position=”near-note” test true, position=”subsequent” also tests true.

```
type
```

Tests whether the item matches the given types ([Appendix III - Types](https://docs.citationstyles.org/en/stable/specification.html#appendix-iii-types)).

```
variable
```

Tests whether the default (long) forms of the given variables ([Appendix IV - Variables](https://docs.citationstyles.org/en/stable/specification.html#appendix-iv-variables)) contain non-empty values.

## 样式行为

### 选项

样式可以使用不同的元素来进行特定的配置。在`cs:citation`元素中设置元素可以配置特定的引文选项；在`cs:bibliography`元素和全局选项（同时影响引文和参考文献条目）中，可以配置特定的参考文献条目。继承的名字选项可以在`cs:style`,`cs:style`和`cs:bibliography`中设置。最后，本地化选项可以在`cs:locale`元素中设置。

#### 引文选项

**消除歧义**

**引用分组**

**引用奔溃**

**标注距离**

#### 参考文献目录选项

**空白**

**参考文献分组**



#### 全局选项

**初始名称的连字符**

**页码范围**

**名字辅助？**

#### 可继承的名称选项

#### 局部选项

### 排序

#### 排序变量

#### 排序宏

### 范围分隔符

### 格式化

### 词缀

### 分隔符

### 显示

### 引用

### Strip-periods

### Text-case

`Text-case` 属性可以在`cs:date`,`cs:date-part`,`cs:label`,`cs:name-part`,`cs:number`和`cs:text`中设置，可设置的值为：

- “lowercase”: renders text in lowercase
- “uppercase”: renders text in uppercase
- “capitalize-first”: capitalizes the first character of the first word, if the word is lowercase
- “capitalize-all”: capitalizes the first character of every lowercase word
- “sentence”: renders text in sentence case
- “title”: renders text in title case

#### 大小写转换

#### 标题大小写转换

## 附录I 学科分类

- anthropology  人类学
- astronomy  天文学
- biology 生物学
- botany 植物学
- chemistry 化学
- communications  通讯
- engineering 工程学
- generic-base - used for generic styles like Harvard and APA  通用基础-用于通用样式，像哈弗和`APA`
- geography 地理学
- geology 地质学
- history 历史
- humanities 人文学科
- law 法学
- linguistics 语言学
- literature 文学
- math 数学
- medicine 药物学
- philosophy 哲学
- physics 物理学
- political_science 政治科学
- psychology 心理学
- science  科学
- social_science  社会科学
- sociology 社会学
- theology 神学
- zoology 动物学

## 附录II 术语

### [Locators](https://docs.citationstyles.org/en/stable/specification.html#id89)

- book
- chapter
- column
- figure
- folio
- issue
- line
- note
- opus
- page
- paragraph
- part
- section
- sub verbo
- verse
- volume

### [Months](https://docs.citationstyles.org/en/stable/specification.html#id90)

- month-01
- month-02
- month-03
- month-04
- month-05
- month-06
- month-07
- month-08
- month-09
- month-10
- month-11
- month-12

### [Ordinals](https://docs.citationstyles.org/en/stable/specification.html#id91)

- ordinal
- ordinal-00 through ordinal-99
- long-ordinal-01
- long-ordinal-02
- long-ordinal-03
- long-ordinal-04
- long-ordinal-05
- long-ordinal-06
- long-ordinal-07
- long-ordinal-08
- long-ordinal-09
- long-ordinal-10

### [Quotation marks](https://docs.citationstyles.org/en/stable/specification.html#id92)

- open-quote
- close-quote
- open-inner-quote
- close-inner-quote

### [Roles](https://docs.citationstyles.org/en/stable/specification.html#id93)

- author
- collection-editor
- composer
- container-author
- director
- editor
- editorial-director
- editortranslator
- illustrator
- interviewer
- original-author
- recipient
- reviewed-author
- translator

### [Seasons](https://docs.citationstyles.org/en/stable/specification.html#id94)

- season-01
- season-02
- season-03
- season-04

### [Miscellaneous](https://docs.citationstyles.org/en/stable/specification.html#id95)

- accessed
- ad
- and
- and others
- anonymous
- at
- available at
- bc
- by
- circa
- cited
- edition
- et-al
- forthcoming
- from
- ibid
- in
- in press
- internet
- interview
- letter
- no date
- online
- presented at
- reference
- retrieved
- scale
- version

## 附录III 文献类型

- article  文章
- article-magazine 杂志文章
- article-newspaper 新闻文章
- article-journal  期刊文章
- bill
- book 书
- broadcast 
- chapter 章节
- dataset  数据集
- entry
- entry-dictionary
- entry-encyclopedia
- figure 图
- graphic
- interview 采访
- legislation
- legal_case
- manuscript
- map  地图
- motion_picture
- musical_score
- pamphlet
- paper-conference
- patent
- post  海报
- post-weblog  
- personal_communication  私人交流
- report  报告
- review
- review-book
- song  歌曲
- speech  演讲
- thesis  学位论文
- treaty
- webpage  网页

## 附录IV 变量

### 标准变量

- abstract

  项目的摘要（例：期刊文章的摘要）

- annote

  读者关于项目内容的笔记

- archive

  保存项目的存档

- archive_location

  存档的位置

- archive-place

  存档的地理位置

- authority

  权力

- call-number

  call number (to locate the item in a library)

- citation-label

  label identifying the item in in-text citations of label styles (e.g. “Ferr78”). May be assigned by the CSL processor based on item metadata.

- citation-number

  index (starting at 1) of the cited reference in the bibliography (generated by the CSL processor)

- collection-title

  title of the collection holding the item (e.g. the series title for a book)

- container-title

  title of the container holding the item (e.g. the book title for a book chapter, the journal title for a journal article)

- container-title-short

  short/abbreviated form of “container-title” (also accessible through the “short” form of the “container-title” variable)

- dimensions

  physical (e.g. size) or temporal (e.g. running time) dimensions of the item

- DOI

  Digital Object Identifier (e.g. “10.1128/AEM.02591-07”)

- event

  name of the related event (e.g. the conference name when citing a conference paper)

- event-place

  geographic location of the related event (e.g. “Amsterdam, the Netherlands”)

- first-reference-note-number

  number of a preceding note containing the first reference to the item. Assigned by the CSL processor. The variable holds no value for non-note-based styles, or when the item hasn’t been cited in any preceding notes.

- genre

  class, type or genre of the item (e.g. “adventure” for an adventure movie, “PhD dissertation” for a PhD thesis)

- ISBN

  International Standard Book Number

- ISSN

  International Standard Serial Number

- jurisdiction

  geographic scope of relevance (e.g. “US” for a US patent)

- keyword

  关键字

- locator

  a cite-specific pinpointer within the item (e.g. a page number within a book, or a volume in a multi-volume work). Must be accompanied in the input data by a label indicating the locator type (see the [Locators](https://docs.citationstyles.org/en/stable/specification.html#locators) term list), which determines which term is rendered by `cs:label` when the “locator” variable is selected.

- medium

  medium description (e.g. “CD”, “DVD”, etc.)

- note

  (short) inline note giving additional item details (e.g. a concise summary or commentary)

- original-publisher

  original publisher, for items that have been republished by a different publisher

- original-publisher-place

  geographic location of the original publisher (e.g. “London, UK”)

- original-title

  最初版本的题目

- page

  项目的页码范围

- page-first

  页码范围的第一个页码

- PMCID

  PubMed Central reference number

- PMID

  PubMed reference number

- publisher

  出版商/出版社

- publisher-place

  出版社的地理位置

- references

  resources related to the procedural history of a legal case

- reviewed-title

  title of the item reviewed by the current item

- scale

  scale of e.g. a map

- section

  container section holding the item (e.g. “politics” for a newspaper article)

- source

  from whence the item originates (e.g. a library catalog or database)

- status

  (publication) status of the item (e.g. “forthcoming”)

- title

  primary title of the item

- title-short

  short/abbreviated form of “title” (also accessible through the “short” form of the “title” variable)

- URL

  链接

- version

  版本

- year-suffix

  disambiguating year suffix in author-date styles (e.g. “a” in “Doe, 1999a”)

### 数据变量

Number variables are a subset of the [Standard Variables](https://docs.citationstyles.org/en/stable/specification.html#standard-variables).

- chapter-number

  章序号

- collection-number

  number identifying the collection holding the item (e.g. the series number for a book)

- edition

  版本序号（注意和version的区别）

- issue

  (container) issue holding the item (e.g. “5” when citing a journal article from journal volume 2, issue 5)

- number

  number identifying the item (e.g. a report number)

- number-of-pages

  引用项目的总的页数

- number-of-volumes

  total number of volumes, usable for citing multi-volume books and such

- volume

  (container) volume holding the item (e.g. “2” when citing a chapter from book volume 2)

### 日期变量

- accessed

  date the item has been accessed

- container

  ?

- event-date

  date the related event took place

- issued

  date the item was issued/published

- original-date

  (issue) date of the original version

- submitted

  date the item (e.g. a manuscript) has been submitted for publication

### 名字变量

- author

  作者

- collection-editor

  editor of the collection holding the item (e.g. the series editor for a book)

- composer

  composer (e.g. of a musical score)

- container-author

  author of the container holding the item (e.g. the book author for a book chapter)

- director

  director (e.g. of a film)

- editor

  编辑

- editorial-director

  managing editor (“Directeur de la Publication” in French)

- illustrator

  illustrator (e.g. of a children’s book)

- interviewer

  interviewer (e.g. of an interview)

- original-author

  ?

- recipient

  recipient (e.g. of a letter)

- reviewed-author

  author of the item reviewed by the current item

- translator

  翻译

## 附录V 页码范围格式

`cs:style`元素中的`page-range-format`表示了页码的缩写规则，其取值可能是：

`"chicago"`

| 第一个数字                           | 第二个数字                                     | 例                                   |
| ------------------------------------ | ---------------------------------------------- | ------------------------------------ |
| 小于100                              | 使用所有的位数                                 | 3–10; 71–72                          |
| 100或者100的整数                     | 使用所有的位数                                 | 100–104; 600–613; 1100–1123          |
| 100到109 （包括对应100的倍数的范围） | 仅使用有变化的部分，忽略不需要的0              | 107–8; 505–17; 1002–6                |
| 110到199 （包括对应100的倍数的范围） | 根据需要使用两位数或者更多                     | 321–25; 415–532; 11564–68; 13792–803 |
| 4位数                                | 如果数字是4位并且其中有3位不同，使用所有的位数 | 1496–1504; 2787–2816                 |

`"expanded"`

​	扩展模式，不适用缩写。eg. 42–45、321–328、2787–2816

`"minimal"`

​	第二个数字中重复的所有数字都被省略：42–5，321–8，2787–816

`"minimal-two"`

​	和`"minimal"`类似，但当第二个数字有两个或者两个以上的数字时，第二个数字至少保留两位。

