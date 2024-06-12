# Cのコーディングルール
これは個人的なルールであり、利用したことによる影響を担保することはできません。
私は実行スピードよりも正確性、読みやすさ、シンプルさ、保守性を優先したルールを自身に定めています。
読みやすさはコードの長さと関数の肥大化、最適化と秩序の有無が影響します。
そのため、これらを考えて書くことで保守と拡張性を高めることができます。
以下のルールは意図を踏まえて、時と場合に合わせて無視することもあります。
その際はコメントとして理由を書きます。

## 現在存在する最も現代的な基準で書くようにすること
常に-std=c18のようにバージョンを書くようにしてください。
これによりエラーを未然に防ぐことができ、互換性を向上させます。
またc18はc89に比べてはるかに優れています。様々な機能が追加され
安全性も向上しています。

## 常に全ての警告を表示してコンパイルすること
```
CFLAGS += -g3                     # Enable level 3 debugging information レベル3のデバッグ情報を有効にする
CFLAGS += -std=c18                # Use the C18 standard C18標準を使用する
CFLAGS += -O3                     # Optimize for maximum speed (level 3 optimization) 最大速度のための最適化（レベル3の最適化）
CFLAGS += -fstack-size-section    # Place the stack size in its own section スタックサイズを独自のセクションに配置する
# CFLAGS += -D_FORTIFY_SOURCE=2    # Enable additional security checks for buffer overflows (optional) バッファオーバーフローのための追加のセキュリティチェックを有効にする（オプション）

WERRORS += -Wall                               # Enable all standard warnings すべての標準的な警告を有効にする
WERRORS += -Wextra                             # Enable extra warnings 追加の警告を有効にする
# WERRORS += -Werror                            # Treat all warnings as errors (optional) すべての警告をエラーとして扱う（オプション）
WERRORS += -Wpedantic                          # Issue all the mandatory diagnostics demanded by strict ISO C 厳密なISO Cで要求されるすべての必須診断を発行する
WERRORS += -Wshadow                            # Warn whenever a local variable shadows another variable ローカル変数が他の変数を隠すときに警告を発する
WERRORS += -Wwrite-strings                     # Warn about deprecated string constants 非推奨の文字列定数について警告する
WERRORS += -Wstrict-prototypes                 # Warn if a function is declared or defined without specifying the argument types 引数の型を指定せずに関数が宣言または定義された場合に警告する
WERRORS += -Wold-style-definition              # Warn if an old-style (K&R) function definition is used 古いスタイル（K&R）の関数定義が使用されている場合に警告する
WERRORS += -Wno-unused-parameter               # Do not warn about unused parameters 未使用のパラメータについて警告しない
WERRORS += -Wredundant-decls                   # Warn if anything is declared more than once in the same scope 同じスコープ内で何かが複数回宣言されている場合に警告する
WERRORS += -Wnested-externs                    # Warn if a function is declared with an extern keyword inside a function 関数内でexternキーワードを使用して関数が宣言されている場合に警告する
WERRORS += -Wmissing-include-dirs              # Warn if an #include directive could not find a file #includeディレクティブがファイルを見つけられない場合に警告する
WERRORS += -Werror=implicit-function-declaration    # Treat implicit function declarations as errors 暗黙の関数宣言をエラーとして扱う
WERRORS += -Werror=int-conversion                   # Treat invalid type conversions as errors 無効な型変換をエラーとして扱う
WERRORS += -Werror=incompatible-pointer-types       # Treat incompatible pointer types as errors 互換性のないポインタ型をエラーとして扱う
WERRORS += -Werror=shift-count-overflow             # Treat shift count overflows as errors シフトカウントオーバーフローをエラーとして扱う
WERRORS += -Werror=switch                           # Treat switch statements without a default case as errors デフォルトケースのないswitch文をエラーとして扱う
WERRORS += -Werror=return-type                      # Treat incorrect return types as errors 不正な戻り値の型をエラーとして扱う
WERRORS += -Werror=pointer-integer-compare          # Treat pointer and integer comparisons as errors ポインタと整数の比較をエラーとして扱う
WERRORS += -Werror=tautological-constant-out-of-range-compare    # Treat tautological comparisons as errors 自明な比較をエラーとして扱う

# GCC warnings that Clang doesn't provide:
ifeq ($(CC),gcc)
    WERRORS += -Wjump-misses-init -Wlogical-op
else ifeq ($(CC),clang)
    WERRORS += -Weverything
endif
```
エラーFlagは-Wall -Wextra -Werrorが標準ですが、上記のようにすれば
十分な数のエラーをつけることができます。
最適化もエラーの発見に役立つため、例外でコンパイルでの最適化を行います。

## タブを使用すると見た目は良くなりますが、統一感がないため全てスペースにすること
タブは利用すると見た目は綺麗になりますが、人間はスペースとタブの違いを瞬時に理解して
処理するようにはできていません。永久に議論されることではありますが、
私はスペースで統一することを選びます。
* Tabキーはエディターに何かを割り当てられていることが多く、Tabバイトの表す長さはOSによって異なる
* ソフトウェアによってTabバイトを処理できない可能性があり、Tabの排除に迫られた時それは困難を極める可能性があるため
デフォルトで設定されたUnixシステム、および古代のダム端末やテレタイプでは、
TAB文字が「現在の列が8の倍数になるまで右に移動する」ことを意味するのが伝統でした。
WindowsとMacのエディタでは、デフォルトの解釈は同じですが、
8の倍数の代わりになぜか4の倍数が使用されます。
ASCIIのTAB文字タブ文字はこれらのスペースを圧縮するために使用されていました。
ですが、現代ではメモリは十分確保できるため圧縮は実行バイナリだけで十分です。

## 1行あたり79文字以内に収めるようにすること

1行あたり80文字は、コードを表示するための事実上の標準です。
複数ウィンドウで並べて配置することができるほか、長い行を読むときに払う多くの労力を避けることができます。
最後の列に常にスペースがあるように、最大79文字は必然の行為です。
右のマージンも提供し、行が複数にわたることを避けることができます。
また、80文字を超えると、コードの読みにくくなります。
読みにくい行が折り返しになるか、読者が最後の数文字を取得するためにウィンドウを右にスクロールする必要があります。
目が次の行の先頭にたどり着くためにさらに移動する必要があるため、長い行を読むのは難しく、
さらに進むほど、視覚的に再調整する必要がある可能性が高くなります。 

## コメントは//を使用して単一行で行う

複数行#defineのインラインコメントには/* ... */を使用する必要があります。
そのほかは//を使用してコメントを簡潔に書くことを意識します。

## 名前空間をコメントで擬似的に持ち込む

名前空間は可読性、エラーの回避において非常に効果的です。
しかしCには存在しません。そのため擬似的に以下のように再現する必要があります。
```
#include <stdio.h>  // fopen() fclose()
#include "module.h" // test()  
```
* コードを読む際にどの関数がどのファイルから読み出されたかgrepを使用して確認する必要がなくなる。
* includeの削除と追加を他の開発で非常に明確に行うことができる。
* 名前の重複を意識することでエラーを未然に防ぐことができる。

## 定義を行うヘッダーファイルは二重インクルードを避けること

```
#pragma once
```
ガードを含めることで、コンパイルを壊すことなくヘッダーファイル「2回」を含めることが可能です。
```
typedef int INT;

struct Point {
    int x;
    int y;
};
```
もしインクルードファイルに宣言のみが含まれている場合は重複は問題ありません。
定義を行う場合は二重インクルードはコンパイルを壊してしまいます。
しかし、ファイルの同一性の証明は難しく、#pragma onceは二重インクルードは避けることはできても
定義の重複は避けることができません。
その点を十分留意した上でインクルードファイルを扱う必要があります。

## グローバル変数と静的変数は可能な限り避けること。

引数の入れ子状態は避ける必要がありますが、グローバル変数は制御が難しい技術です。
また、大規模になるにつれて管理もエラーハンドリングも難しくなります。
可変のグローバル変数は特に悪であり、どんな犠牲を払っても避けるべきです。
概念的には、グローバル変数の割り当ては、隠された静的変数を設定するためのlongjmpの束です。
常に、引数によって完全に制御できるように関数を設計しなければなりません。

しかし、可読性に一定の寄与を行いグローバル変数が非常に少なく抑えられる場合のみ
利用を考慮し、利用した場合はコメントで理由を記載します。

関数内の静的変数はパフォーマンスには寄与しますが、実態はグローバル変数と同等の制御が難しい技術です。
数が非常に少なく、外部との連携としてどうしても必要な場合以外の利用は避けてください。
永続的なものを返す必要がある場合はメモリを割り当てます。

## 常にconstを使用する

## 特定の理由がない限り、floatではなくdoubleを使用すること

## 定数は関数の先頭に、変数は使用直前に宣言すること

## 関数のプロトタイプで引数名は外部から読み込まれる場合は必ず書くこと

## floatが早くても小数点エラーの観点からdoubleを使用すること

## 変数宣言は全体で使う場合は先頭に局所的に使用する場合は直前に行うこと

## 変数定義では初期化を含め、必ず一行につき1変数のみにすること

## 使用する回数が多い変数ほど短い変数名を意識すること。

コメントを書くことで補えば略称は問題にはなりません。

## 関数間での受け渡しでの変数名は一貫性を保つこと

## bool値に適した場面では必ず使用すること

c23ではbool値は#includeなしで使用ができます。
C17が最小限の変更でC23に対応するなら、定義済みマクロを追加します。　
```
#define bool _Bool
#define false ((bool)+0)
#define true ((bool)+1)
```
意図しないオーバーフローをキャストで解決します。

## 条件式は非明示的な書き方では無く、常に明示的な書き方をすること

最近のコンパイラは非常に優秀です。
少しの書き方の違いでコンパイル時間に差が出ることはあっても、
実行速度に差が出ることはほとんどありません。
それならわかりやすい方がいいですよね。
```
if (x);
if (x > 0);

return !str;
return (str == NULL);
```
NULLは書くべきですが　ture falseは行が長くなるため私はif文でのみ書きます。

## 式内部で処理を行う場合は左から右に書くようにすること(代入や++は🙅‍♀️)

非常に読みにくいコードは処理の前後の混乱です。
右から左に処理をかける文法があるので利用します。
```
char_add( *c, ++x);
char_add( *c, x + 1 );

while ( --num > 0);
while ( num -= 1,
        num > 0 );
```

```
for ( int x = 0, num = get_num( m ); x < num; x++);

int const num = get_num( m )
int x = 0;
for (; x < num; x += 1);
```

## 関数名は初期化はinit_、条件チェックではis_、ある場面での処理はat_とすること

```
init_list

is_alphabet

at_start
at_end
```

## 関数の出力はできる限り自明的な変数に代入すること

is_~といった関数名から自明の返り値がわかる関数が省略します。
そのほかの関数は特に条件式に値を利用する場合はできる限り返り値がどのような値なのか
変数名で示してください。

　