# オペレーティングシステムの設計とコーディングのルール

## カーネルは小さく軽くしなければならない。

## コモンクライテリア（CC）の要件を十分に理解する。

[コモンクライテリア](https://ja.wikipedia.org/wiki/コモンクライテリア)

## Capability-based securityを考え設計を行う。

  Microkernels reduce the attack surface to a minimum
  Capabilities instead of ambient authority mean you don't have to trust code
  MULTICS, KeyKOS, EROS are historical examples, and now Genode and HURD are rising
  If it's got a GUI, it'll have "PowerBoxes" which return capabilities

[Capability-based security](https://ja.wikipedia.org/wiki/Capability-based_security)


## 参考文献

[レッドリーフ OS](https://mars-research.github.io/projects/redleaf/)
