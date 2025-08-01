<!-- Copyright 2000-2025 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# Incompatible Changes in IntelliJ Platform and Plugins API 2025.*

<!--
Before documenting a breaking API change, please make sure that the change cannot be avoided in an alternative way.

APIs marked with @Deprecated(forRemoval=true), @ApiStatus.Experimental, @ApiStatus.Internal/IntellijInternalApi, or @ApiStatus.ScheduledForRemoval don't need to be documented.

To document a new incompatible change, add a new line with the problem pattern followed by a 2nd line with ": "-prefixed human-readable description
and recommended fix/action (REQUIRED, please write full sentence ending with '.', see existing entries as reference).
Non-platform changes must be grouped under relevant section for plugin.

The following problem patterns are supported and must be followed EXACTLY (e.g., no '#' instead of '.'):

<package name> package removed

<class name> class removed
<class name> class renamed to <new class name>
<class name> class moved to package <package name>

<class name>.<method name>(<human-readable parameters>) marked abstract
<class name>.<method name>(<human-readable parameters>) abstract method added
<class name>.<method name>(<human-readable parameters>) method removed
<class name>.<method name>(<human-readable parameters>) method moved to the superclass
<class name>.<method name>(<human-readable parameters>) method return type changed from <before> to <after>
<class name>.<method name>(<human-readable parameters>) method visibility changed from <before> to <after>
<class name>.<method name>(<human-readable parameters>) method marked final
<class name>.<method name>(<human-readable parameters>) method parameter <type> removed
<class name>.<method name>(<human-readable parameters>) method parameter type changed from <before> to <after>
<class name>.<method name> method <parameter name> parameter marked @<class name>
<class name> (class|interface) now (extends|implements) <class name> and inherits its final method <method name>(<human-readable parameters>)?
<class name> (class|interface) now (extends|implements) <class name> and inherits its abstract method <method name>(<human-readable parameters>)?
<class name> class now interface

<class name>(<human-readable parameters>) constructor removed
<class name>(<human-readable parameters>) constructor parameter <type> removed
<class name>(<human-readable parameters>) constructor parameter type changed from <before> to <after>
<class name>(<human-readable parameters>) constructor visibility changed from <before> to <after>

<class name>.<field name> field removed
<class name>.<field name> field moved to the superclass
<class name>.<field name> field type changed from <before> to <after>
<class name>.<field name> field visibility changed from <before> to <after>

<property name> property removed from resource bundle <bundle name>

Where the placeholders must be enclosed in code quotes (`name`):

<class name> is a fully-qualified name of the class, e.g. `com.intellij.openapi.actionSystem.AnAction$InnerClass`.
<method name> is the exact method's name. Note that constructors have dedicated patterns.
<human-readable parameters> is a string representing parameters, which are not necessarily fully qualified. They do not affect the parser. For example, instead of (java.lang.Object, java.util.List, int) you are free to write (Object, List<String>, int)
<parameter name> is exact name of the method's parameter
<property name> is a full name of a property from .properties file, like `some.action.description`
<bundle name> is a fully qualified name of the property bundle, which includes its package, like `message.IdeBundle`

NOTE: If a code change you're trying to document doesn't match any of the above patterns, please ask in #plugins-verifier

NOTE: You are allowed to prettify the pattern using links: [`org.example.Foo`](https://github.com/JetBrains/intellij-community/tree/master/)

NOTE: Entries not starting with code quotes (`name`) can be added to document non-code changes and will be skipped in API verification.
-->

<link-summary>List of known Breaking API Changes in 2025.*</link-summary>

<include from="snippets.topic" element-id="apiChangesHeader"/>

<include from="snippets.topic" element-id="apiChangesJavaVersion"/>

<include from="snippets.topic" element-id="gradlePluginVersion"/>

## 2025.2

### IntelliJ Platform 2025.2

`icons.JavaUltimateIcons` class moved to package `com.intellij.java.ultimate.icons`
: Update code usages and make sure your plugin [depends](plugin_dependencies.md) on the Java plugin.

`com.intellij.psi.xml.HtmlFileElementType.getHtmlStubVersion()` method removed
: Use `com.intellij.xml.HtmlLanguageStubVersionUtil.getHtmlStubVersion()` instead.

`com.intellij.openapi.actionSystem.ActionGroup.getChildren(AnActionEvent, ActionManager)` method removed
: Avoid expanding an action group manually

`com.intellij.openapi.actionSystem.DefaultActionGroup.getChildren(AnActionEvent, ActionManager)` method removed
: Avoid expanding an action group manually or use `DefaultActionGroup.getChildren(ActionManager)`

`com.intellij.diff.util.ThreeSide.map(Function)` method parameter type changed from `com.intellij.util.Function` to `kotlin.jvm.functions.Function1`
: Use `kotlin.jvm.functions.Function1` as a parameter.

The experimental `com.intellij.webSymbols` package together with all the classes and extension points has been renamed to `com.intellij.polySymbols`.
: The classes were renamed using pattern `WebSymbol[s]?(.*)` -> `PolySymbol$1`. Some classes have also been moved
to more specific packages.
: From other notable changes:
1. `PolySymbol` interface no longer extends `PolySymbolScope`
2. `PolySymbol` interface no longer contains documentation-specific properties (`description`, `docUrl`, `sections` and `defaultValue`),
   instead `getDocumentationTarget` method should be overridden and the new `PolySymbolDocumentationTarget.create` method
   used to lazily build documentation for the symbol
3. `PolySymbol` no longer has `abstract`, `required` and `virtual` properties. They've been replaced by a more
   generic `modifiers` property
4. `PolySymbol` no longer has `properties` property, which returned a map of properties. It's been replaced by
   `get` method, which should return value for the requested property.
5. `PolySymbol` no longer has `attributeValue` property, instead `get` method should be overridden to return
   the value for `com.intellij.polySymbols.html.PROP_HTML_ATTRIBUTE_VALUE` property
6. The `WebSymbolsQueryConfigurator$getScope` has been removed and replaced with a pattern-based `PolySymbolQueryScopeContributor` API
7. The `PolySymbolDelegate` and `PsiSourcePolySymbolDelegate` are interfaces now
8. All APIs have been refactored to use `PolySymbolQualifiedKind` instead of separate parameters `namespace` and `kind`
9. Builder patterns have been introduced in various classes, like `PolySymbolQueryExecutor`, `PolySymbolQueryParams`, or `PolySymbolDocumentation`
   instead of now removed Kotlin methods with default parameters.
: Please note that the module is under active development and further major API changes are to be expected in the upcoming releases.

### Package Checker 2025.2

`com.intellij.packageChecker.api.PackageDeclaration(Package)` method parameter type changed from `org.jetbrains.security.package.Package` to `com.intellij.packageChecker.model.Package`
: Use `com.intellij.packageChecker.model.Package` as a parameter.

`com.intellij.packageChecker.api.PackageDeclaration(Package, PsiElement, TextRange)` method parameter type changed from `org.jetbrains.security.package.Package` to `com.intellij.packageChecker.model.Package`
: Use `com.intellij.packageChecker.model.Package` as a parameter.

`com.intellij.packageChecker.api.PackageDeclaration.getPkg()` method return type changed from `org.jetbrains.security.package.Package` to `com.intellij.packageChecker.model.Package`
: Update code usages.

`com.intellij.packageChecker.PackagesInterner.intern(Package)` method parameter type changed from `org.jetbrains.security.package.Package` to `com.intellij.packageChecker.model.Package`
: Use `com.intellij.packageChecker.model.Package` as a parameter.

`com.intellij.packageChecker.PackagesInterner.intern(Package)` method return type changed from `org.jetbrains.security.package.Package` to `com.intellij.packageChecker.model.Package`
: Update code usages.

### JavaScript and TypeScript Plugin 2025.2

`com.intellij.lang.javascript.JSStubElementTypes.DEFINITION_EXPRESSION` field type changed from `com.intellij.lang.javascript.psi.JSStubElementType` to `com.intellij.lang.javascript.psi.JSElementType`
: Recompile code usages.

`com.intellij.lang.javascript.JSStubElementTypes.XML_ATTRIBUTE` field type changed from `com.intellij.psi.stubs.IStubElementType` to `com.intellij.psi.tree.IElementType`
: Recompile code usages.

`com.intellij.lang.javascript.JSStubElementTypes.XML_JS_SCRIPT` field type changed from `com.intellij.psi.tree.IElementType` to `com.intellij.lang.javascript.psi.JSElementType`
: Recompile code usages.

`com.intellij.lang.javascript.JSStubElementTypes.ES6_EMBEDDED_CONTENT` field type changed from `com.intellij.psi.tree.IElementType` to `com.intellij.lang.javascript.psi.JSElementType`
: Recompile code usages.

`com.intellij.lang.javascript.JSStubElementTypes.VARIABLE` field type changed from `com.intellij.lang.javascript.psi.JSStubElementType` to `com.intellij.lang.javascript.psi.JSElementType`
: Recompile code usages.

`com.intellij.lang.javascript.JSStubElementTypes.LITERAL_EXPRESSION` field type changed from `com.intellij.lang.javascript.psi.JSStubElementType` to `com.intellij.lang.javascript.psi.JSElementType`
: Recompile code usages.

`com.intellij.lang.javascript.JSStubElementTypes.EMBEDDED_EXPRESSION` field type changed from `com.intellij.psi.tree.IElementType` to `com.intellij.lang.javascript.psi.JSElementType`
: Recompile code usages.

`com.intellij.lang.typescript.TypeScriptStubElementTypes.TYPESCRIPT_VARIABLE` field type changed from `com.intellij.lang.javascript.psi.JSStubElementType` to `com.intellij.lang.javascript.psi.JSElementType`
: Recompile code usages.

`com.intellij.lang.javascript.JSStubElementTypes.ARRAY_LITERAL_EXPRESSION` field type changed from `com.intellij.lang.javascript.psi.JSStubElementType` to `com.intellij.lang.javascript.psi.JSElementType`
: Recompile code usages.

`com.intellij.lang.javascript.JSStubElementTypes.CALL_EXPRESSION` field type changed from `com.intellij.lang.javascript.psi.JSStubElementType` to `com.intellij.lang.javascript.psi.JSElementType`
: Recompile code usages.

`com.intellij.lang.ecmascript6.ES6StubElementTypes.FIELD_STATEMENT` field type changed from `com.intellij.lang.javascript.psi.JSStubElementType` to `com.intellij.lang.javascript.psi.JSElementType`
: Recompile code usages.

`com.intellij.lang.javascript.JSStubElementTypes.EMBEDDED_CONTENT` field type changed from `com.intellij.psi.tree.IElementType` to `com.intellij.lang.javascript.psi.JSElementType`
: Recompile code usages.

`org.jetbrains.vuejs.lang.expr.VueJSLanguage.Companion` class removed
: Use `org.jetbrains.vuejs.lang.expr.VueJSLanguage` instead.

### Kubernetes Plugin 2025.2

`com.intellij.kubernetes.api.KubernetesApiProvider.getInstance(Project)` method return type changed from `com.intellij.kubernetes.api.KubernetesApiProvider` to `com.intellij.kubernetes.api.KubernetesApiProviderInterface`
: Update code usages.

`com.intellij.kubernetes.api.KubernetesApiProvider.State`class renamed to `com.intellij.kubernetes.api.KubernetesApiProviderInterface.KubeconfigState`
: Update code usages.

## 2025.1

### IntelliJ Platform 2025.1

Code scheduled with `SwingUtilities.invokeLater` and `SwingUtilities.invokeAndWait` does not hold the write-intent lock
: Consider using an explicit wrapping with [`ReadAction.compute()`](%gh-ic%/platform/core-api/src/com/intellij/openapi/application/ReadAction.java) or [`WriteAction.run(ThrowableRunnable<E>)`](%gh-ic%/platform/core-api/src/com/intellij/openapi/application/WriteAction.java).
See [](threading_model.md).

Coroutines running under `Dispatchers.Main` do not hold the write-intent lock
: To restore the old behavior, consider using `Dispatchers.EDT`.
See [](threading_model.md).

`com.intellij.psi.xml.XmlTokenType` class now interface
: Use `XmlTokenType` constants directly.

Class `com.intellij.psi.xml.XmlElementType` no longer extends `com.intellij.psi.xml.XmlTokenType`
: Update code usages.

`filetype.xml.description` property removed from resource bundle `messages.XmlPsiBundle`
: Use property from resource bundle `messages.XmlParserBundle`

`xml.parsing.closing.tag.matches.nothing` property removed from resource bundle `messages.XmlPsiBundle`
: Use property from resource bundle `messages.XmlParserBundle`

`xml.parsing.unclosed.attribute.value` property removed from resource bundle `messages.XmlPsiBundle`
: Use property from resource bundle `messages.XmlParserBundle`

`xml.parsing.unescaped.ampersand.or.nonterminated.character.entity.reference` property removed from resource bundle `messages.XmlPsiBundle`
: Use property from resource bundle `messages.XmlParserBundle`

### Database Plugin 2025.1

`com.intellij.database.view.models` package removed
: Old table modification dialog was finally removed. Use `com.intellij.database.actions.ddl.ModifyObjectAction.showDialog()` for new dialog invocation.

`com.intellij.database.schemaEditor.model.DeObject` class removed
: Old table modification dialog was finally removed. Use `com.intellij.database.actions.ddl.ModifyObjectAction.showDialog()` for new dialog invocation.

`com.intellij.database.view.editors.DatabaseEditorContext` class removed
: Old table modification dialog was finally removed. Use `com.intellij.database.actions.ddl.ModifyObjectAction.showDialog()` for new dialog invocation.

`com.intellij.database.view.editors.DatabaseTableEditor` class removed
: Old table modification dialog was finally removed. Use `com.intellij.database.actions.ddl.ModifyObjectAction.showDialog()` for new dialog invocation.

`com.intellij.database.view.ui.DbRefactoringDialogHelper` class removed
: Old table modification dialog was finally removed. Use `com.intellij.database.actions.ddl.ModifyObjectAction.showDialog()` for new dialog invocation.

`com.intellij.database.view.ui.DbTableDialog` class removed
: Old table modification dialog was finally removed. Use `com.intellij.database.actions.ddl.ModifyObjectAction.showDialog()` for new dialog invocation.

### External System 2025.1

`com.intellij.openapi.externalSystem.service.internal.ExternalSystemProcessingManager` class now interface
: Recompile code usages.

### JavaScript and TypeScript Plugin 2025.1

`com.intellij.lang.javascript.service.JSAsyncLanguageServiceBase.JSLanguageServiceInfoReporter` class moved to package `com.intellij.lang.javascript.service`
: Update all usages

`com.intellij.lang.javascript.JSElementTypes.toModuleContentType(IElementType type)` method removed
: Use `com.intellij.lang.javascript.JSModuleContentType.toModuleContentType(IElementType type)` method instead

`com.intellij.lang.javascript.BaseJSTokenTypes` class removed
: Use `com.intellij.lang.javascript.JSTokenTypes` class instead

`com.intellij.lang.javascript.highlighting.TypeScriptHighlighter(DialectOptionHolder dialectOptionsHolder, boolean skipKeywordHighlights)` constructor removed
: Use constructor without `skipKeywordHighlights` parameter

`com.intellij.lang.javascript.dialects.ECMA6SyntaxHighlighterFactory.ECMA6SyntaxHighlighter(DialectOptionHolder dialectOptionsHolder, boolean skipKeywordHighlights)` constructor removed
: Use constructor without `skipKeywordHighlights` parameter

`com.intellij.lang.javascript.highlighting.JSHighlighter(DialectOptionHolder dialectOptionsHolder, boolean skipKeywordHighlights)` constructor removed
: Use constructor without `skipKeywordHighlights` parameter

### Kotlin Plugin 2025.1

`org.jetbrains.kotlin.KtFakeSourceElement` class renamed to `org.jetbrains.kotlin.KtFakePsiSourceElement`
: Update code usages.

`org.jetbrains.kotlin.ir.linkage.IrDeserializer.IrLinkerExtension` class removed
: This class was removed from the Kotlin compiler and is no longer available.

`org.jetbrains.kotlin.ir.builders.TranslationPluginContext` class removed
: This class was removed from the Kotlin compiler and is no longer available.

`org.jetbrains.kotlin.analysis.decompiler.stub.file.ClsClassFinder.isKotlinInternalCompiledFile$default(ClsClassFinder, VirtualFile, byte[], int, Object)` method removed
: Recompile code usages.

### Remote Development 2025.1

`com.jetbrains.rd.ide.model.AddToGroupRuleModel` class removed
: Remove all usages.

`com.jetbrains.rd.ide.model.ActionConstraintModel` class removed
: Remove all usages.

### Terminal Plugin 2025.1

`com.jediterm.terminal.model.TextBufferChangesListener.historyCleared()` abstract method added
: Must be implemented.

`com.intellij.terminal.ui.TerminalWidget.connectToSession(TerminalSession session)` abstract method added
: Must be implemented.

`com.intellij.terminal.ui.TerminalWidget.getSession()` abstract method added
: Must be implemented.

`com.intellij.terminal.ui.TerminalWidget.getTerminalSizeInitializedFuture()` abstract method added
: Must be implemented.
