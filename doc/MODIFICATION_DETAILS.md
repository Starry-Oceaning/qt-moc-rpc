# 修改详情

本文档详细说明了对Qt 5.15.5 moc源代码的每一处修改。

## 修改文件概览

| 文件 | 修改行数 | 主要修改内容 |
|------|----------|--------------|
| `token.h` | +1 | 添加新的token类型 `TOKEN_RPC_MODULE` |
| `preprocessor.cpp` | +48 | 增加识别关键字`Q_RPC_MODULE` |
| `moc.h` | +1 | 添加`hasQRpc`成员变量 |
| `moc.cpp` | +6 | 识别是否使用`Q_RPC_MODULE`宏 |
| `generator.h` | +2 | 添加`generateRpcTypeRegister`函数声明与`customType`成员变量 |
| `generator.cpp` | +28 | 获取函数使用的类型以及生成对这些类型的注册代码 |

**总计修改：约86行代码**

---

## 详细修改内容

### 1. `token.h` - 添加新的token类型

```diff
#define FOR_ALL_TOKENS(F) \
    F(NOTOKEN) \
    F(IDENTIFIER) \
	......
     F(Q_PRIVATE_PROPERTY_TOKEN) \
+	F(Q_RPC_MODULE_TOKEN) \
     F(Q_REVISION_TOKEN) \
	...... 
};
```

### 2. `keywords.cpp` - 增加识别关键字

```diff
 static const short keyword_trans[][128] = {
-    {0,0,0,0,0,0,0,0,0,561,558,0,0,0,0,0,
+    {0,0,0,0,0,0,0,0,0,571,568,0,0,0,0,0,
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
-     561,252,559,562,8,38,239,560,25,26,236,234,30,235,27,237,
+     571,252,569,572,8,38,239,570,25,26,236,234,30,235,27,237,
      22,22,22,22,22,22,22,22,22,22,34,41,23,39,24,43,
      0,8,8,8,8,8,8,8,8,8,8,8,8,8,8,8,
-     8,21,8,8,8,8,8,8,8,8,8,31,564,32,238,8,
+     8,21,8,8,8,8,8,8,8,8,8,31,574,32,238,8,
      0,1,2,3,4,5,6,7,8,9,8,8,10,11,12,13,
      14,8,15,16,17,18,19,20,8,8,8,36,245,37,248,0},
     {0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
	 ......
     {0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
      0,0,0,0,0,0,0,0,0,0,42,0,0,0,28,0,
-     567,567,567,567,567,567,567,567,567,567,0,0,0,0,0,0,
+     577,577,577,577,577,577,577,577,577,577,0,0,0,0,0,0,
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
	 ......
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
     {0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
-     0,0,0,0,0,0,0,0,0,0,566,0,0,0,0,565,
+     0,0,0,0,0,0,0,0,0,0,576,0,0,0,0,575,
      0,0,0,0,0,0,0,0,0,0,0,0,0,258,0,0,
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
	 ......
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
      542,0,0,510,0,0,0,0,0,0,0,0,0,0,0,0,
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
+     0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0},
+    {0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
+     0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
+     0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
+     0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
+     0,0,0,0,0,561,0,0,0,0,0,0,0,0,0,0,
+     551,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
+     0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,
      0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0}
 };
 
static const struct
{
   Token token;
   short next;
   char defchar;
   short defnext;
   Token ident;
} keywords[] = {
     {NOTOKEN, 0, 0, 0, NOTOKEN},
	 ...... 	
     {CHARACTER, 0, 84, 548, CHARACTER},
     {CHARACTER, 0, 89, 549, CHARACTER},
     {Q_PRIVATE_PROPERTY_TOKEN, 0, 0, 0, CHARACTER},
-    {CHARACTER, 0, 69, 551, CHARACTER},
-    {CHARACTER, 0, 86, 552, CHARACTER},
-    {CHARACTER, 0, 73, 553, CHARACTER},
-    {CHARACTER, 0, 83, 554, CHARACTER},
-    {CHARACTER, 0, 73, 555, CHARACTER},
-    {CHARACTER, 0, 79, 556, CHARACTER},
-    {CHARACTER, 0, 78, 557, CHARACTER},
+    {CHARACTER, 52, 0, 0, CHARACTER},
+    {CHARACTER, 0, 67, 552, CHARACTER},
+    {CHARACTER, 0, 95, 553, CHARACTER},
+    {CHARACTER, 0, 77, 554, CHARACTER},
+    {CHARACTER, 0, 79, 555, CHARACTER},
+    {CHARACTER, 0, 68, 556, CHARACTER},
+    {CHARACTER, 0, 85, 557, CHARACTER},
+    {CHARACTER, 0, 76, 558, CHARACTER},
+    {CHARACTER, 0, 69, 559, CHARACTER},
+    {Q_RPC_MODULE_TOKEN, 0, 0, 0, CHARACTER},
+    {CHARACTER, 0, 69, 561, CHARACTER},
+    {CHARACTER, 0, 86, 562, CHARACTER},
+    {CHARACTER, 0, 73, 563, CHARACTER},
+    {CHARACTER, 0, 83, 564, CHARACTER},
+    {CHARACTER, 0, 73, 565, CHARACTER},
+    {CHARACTER, 0, 79, 566, CHARACTER},
+    {CHARACTER, 0, 78, 567, CHARACTER},
     {Q_REVISION_TOKEN, 0, 0, 0, CHARACTER},
     {NEWLINE, 0, 0, 0, NOTOKEN},
     {QUOTE, 0, 0, 0, NOTOKEN},
     {SINGLEQUOTE, 0, 0, 0, NOTOKEN},
     {WHITESPACE, 0, 0, 0, NOTOKEN},
-    {HASH, 0, 35, 563, HASH},
+    {HASH, 0, 35, 573, HASH},
     {PP_HASHHASH, 0, 0, 0, NOTOKEN},
     {BACKSLASH, 0, 0, 0, NOTOKEN},
     {CPP_COMMENT, 0, 0, 0, NOTOKEN},
     {C_COMMENT, 0, 0, 0, NOTOKEN},
     {FLOATING_LITERAL, 0, 0, 0, NOTOKEN}
};
```

### 3. `moc.h` - 添加成员变量

```diff
struct ClassDef : BaseDef {
{
     ......
     bool hasQObject = false;
     bool hasQGadget = false;
+    bool hasQRpc = false;
};
```

### 4. `moc.cpp` - 设置标志

```diff
void Moc::parse()
{
    QVector<NamespaceDef> namespaceList;
    bool templateClass = false;
    while (hasNext()) {
        Token t = next();
        switch (t) {
			......
            case STRUCT: {
                if (currentFilenames.size() <= 1)
                    break;

                ClassDef def;
                if (!parseClassHead(&def))
                    continue;

                while (inClass(&def) && hasNext()) {
                    switch (next()) {
+                   case Q_RPC_MODULE_TOKEN:
+                        def.hasQRpc = true;
+                        break;
                    case Q_OBJECT_TOKEN:
                        def.hasQObject = true;
                        break;
                    case Q_GADGET_TOKEN:
                        def.hasQGadget = true;
                        break;
                    default: break;
                    }
                }

                if (!def.hasQObject && !def.hasQGadget)
                    continue;

                for (int i = namespaceList.size() - 1; i >= 0; --i)
                    if (inNamespace(&namespaceList.at(i)))
                        def.qualified.prepend(namespaceList.at(i).classname + "::");

                QHash<QByteArray, QByteArray> &classHash = def.hasQObject ? knownQObjectClasses : knownGadgets;
                classHash.insert(def.classname, def.qualified);
                classHash.insert(def.qualified, def.qualified);

                continue; }
			......	
		}
		......
		if (parseClassHead(&def)) {
            FunctionDef::Access access = FunctionDef::Private;
            for (int i = namespaceList.size() - 1; i >= 0; --i)
                if (inNamespace(&namespaceList.at(i)))
                    def.qualified.prepend(namespaceList.at(i).classname + "::");
            while (inClass(&def) && hasNext()) {
                switch ((t = next())) {
				.....
+               case Q_RPC_MODULE_TOKEN:
+                    def.hasQRpc = true;
+                    break;
                case Q_OBJECT_TOKEN:
                    def.hasQObject = true;
                    if (templateClass)
                        error("Template classes not supported by Q_OBJECT");
                    if (def.classname != "Qt" && def.classname != "QObject" && def.superclassList.isEmpty())
                        error("Class contains Q_OBJECT macro but does not inherit from QObject");
                    break;
				.....	
				}
			}
		}
		......	
	}
}
```

### 5. `generator.h` - 添加函数声明与成员变量

```diff
class Generator
{
public:
     ......
+    void generateRpcTypeRegister();
	 ......
+    QSet<QByteArray> customType;
};
```

### 6. `generator.cpp` - 获取函数使用的类型以及生成对这些类型的注册代码

```diff
void Generator::generateCode()
{
    ......
    generatePluginMetaData();

//
+// Generate rpc type register
+//
+    generateRpcTypeRegister();
+//
// Generate function to make sure the non-class signals exist in the parent classes
   ......
}

void Generator::registerFunctionStrings(const QVector<FunctionDef>& list)
{
    for (int i = 0; i < list.count(); ++i) {
        const FunctionDef &f = list.at(i);

        strreg(f.name);
-        if (!isBuiltinType(f.normalizedType))
+        if (!isBuiltinType(f.normalizedType)) {
             strreg(f.normalizedType);
+            QByteArray strType(f.normalizedType);
+            customType.insert(strType.replace('&',""));
+        }
+

        strreg(f.tag);

        int argsCount = f.arguments.count();
        for (int j = 0; j < argsCount; ++j) {
            const ArgumentDef &a = f.arguments.at(j);
-            if (!isBuiltinType(a.normalizedType))
+            if (!isBuiltinType(a.normalizedType)) {
                 strreg(a.normalizedType);
+                QByteArray strType(a.normalizedType);
+                customType.insert(strType.replace('&',""));
+            }
            strreg(a.name);
        }
    }
}

+void Generator::generateRpcTypeRegister()
+{
+    if (!cdef->hasQRpc) {
+        return;
+    }
+    fprintf(out, "\nvoid %s::registerRpcType()\n", cdef->qualified.constData());
+    fprintf(out, "{\n");
+    fprintf(out, "    %s::registerRpcType();", purestSuperClass.constData());
+    for(auto type: customType)
+    {
+        fprintf(out, "\n    qRpcRegisterMetaType<%s>(\"%s\");", type.constData(), type.constData());
+    }
+    fprintf(out, "\n}\n");
+}
+
```