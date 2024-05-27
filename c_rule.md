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
CFLAGS += -g3 -std=c18
CFLAGS += -O3
CFLAGS += -fstack-size-section
# CFLAGS += -D_FORTIFY_SOURCE=2

WERRORS += -Wall -Wextra
# WERRORS += -Werror
WERRORS += -Wpedantic
WERRORS += -Wshadow
WERRORS += -Wwrite-strings
WERRORS += -Wstrict-prototypes
WERRORS += -Wold-style-definition
WERRORS += -Wno-unused-parameter
WERRORS += -Wredundant-decls
WERRORS += -Wnested-externs
WERRORS += -Wmissing-include-dirs
WERRORS += -Werror=implicit-function-declaration
WERRORS += -Werror=int-conversion
WERRORS += -Werror=incompatible-pointer-types
WERRORS += -Werror=shift-count-overflow
WERRORS += -Werror=switch
WERRORS += -Werror=return-type
WERRORS += -Werror=pointer-integer-compare
WERRORS += -Werror=tautological-constant-out-of-range-compare


# GCC warnings that Clang doesn't provide:

ifeq ($(CC),gcc)
    WERRORS += -Wjump-misses-init -Wlogical-op
else ifeq ($(CC),clang)
    WERRORS += -Weverything
endif

# Compiling with optimizations
CFLAGS += -O2
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



　
