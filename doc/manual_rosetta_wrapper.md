# Rosettaラッパーライブラリ


## 概要
- Rosettaソフトウェアの計算プロトコルはpythonベースだが、ユーザが直接設定・計算実行する上では複雑な箇所が多い。
- その設定・計算実行を簡易的に扱うためのラッパーライブラリである。
- 基本的には、[Y-Github][Y-Github]のリポジトリにマニュアルがすでにあるため、それを参照されたい。

## 構成
- 主に下記4種類のラッパーライブラリが用意されている。
    - MPIExecutor：MPI実行周り
    - Scorer：スコア計算周り
    - CartDDG：cartesian_ddg周り
    - Relax：relax計算周り
- [Y-Github][Y-Github]のリポジトリに、これらをテストするためのコードも用意されているため、詳細はそちらを参照されたい。

### MPIExecutor
- MPIにノードを追加し、並列実行することが可能。

### Scorer
- スコア計算を実行できる。
- pdbデータをparseできる。
> import denovoutil.rosetta.pdb as PDB
> m2 = PDB.parse('2cbh.pdb')
- parseしたpdbデータについて、スコア関数を指定し、スコア計算を実行できる。
> import denovoutil.rosetta.scorer as Scorer
> scorer = Scorer.create()
> sc1 = scorer.run(m2, 'ref2015')
> sc2 = scorer.run(m2, 'ref2015_cart')

## インストール



[Y-Github]:https://yghe.amzn.ykgw.net/denovo/denovoutil
