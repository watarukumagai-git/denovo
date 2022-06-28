# Rosettaラッパーライブラリ


## 概要
- Rosettaソフトウェアの計算プロトコルはpythonベースだが、ユーザが直接設定・計算実行する上では複雑な箇所が多い。
- その設定・計算実行を簡易的に扱うためのラッパーライブラリである。
- 基本的には、[Y-Github][Y-Github]のリポジトリにマニュアルがすでにあるため、それを参照されたい。

## 構成
- 主に下記のラッパーライブラリが用意されている。
    - MPIExecutor：MPI実行周り
    - PDB：pdbデータ周り
    - Scorer：スコア計算周り
    - Relax：relax計算周り
    - CartDDG：cartesian_ddg周り
- [Y-Github][Y-Github]のリポジトリに、これらをテストするためのコードも用意されているため、詳細はそちらを参照されたい。

### MPIExecutor
- MPIにノードを追加し、並列実行することが可能。
```
from denovoutil.common import mpiexecutor

mpiexec = mpiexecutor.create()
mpiexec.add_node('enzcpu04', 48)
mpiexec.add_node('enzcpu05', 48)
```

### PDB
- pdbデータのparseが可能。
```
import denovoutil.rosetta.pdb as PDB
m2 = PDB.parse('2cbh.pdb')
```

### Scorer
- parseしたpdbデータについて、スコア関数を指定し、スコア計算を実行できる。
```
import denovoutil.rosetta.pdb as PDB
import denovoutil.rosetta.scorer as Scorer

m2 = PDB.parse('2cbh.pdb')

scorer = Scorer.create()
sc1 = scorer.run(m2, 'ref2015')
sc2 = scorer.run(m2, 'ref2015_cart')
```

### Relax
- parseしたpdbデータについて、relax計算を実行し、その構造とスコアを得る。
```
from denovoutil.common import mpiexecutor
import denovoutil.rosetta.pdb as PDB
import denovoutil.rosetta.relax as Relax

mpiexec = mpiexecutor.create()
mpiexec.add_node('enzcpu04', 48)
mpiexec.add_node('enzcpu05', 48)

m1 = PDB.parse('sample/1cbh.pdb')[0]

nstruct = 4
relax = Relax.create(mpiexec)
models, scores = relax.run(m1, nstruct)
```

### CartDDG
- parseしたpdbデータについて、cartesian_ddgを実行し、そのスコアを得る。
```
from denovoutil.common import mpiexecutor
import denovoutil.rosetta.pdb as PDB
import denovoutil.rosetta.cartddg as CartesianDDG

mpiexec = mpiexecutor.create()
mpiexec.add_node('enzcpu04', 48)
mpiexec.add_node('enzcpu05', 48)

m1 = PDB.parse('sample/1cbh.pdb')

ddg = CartesianDDG.create(mpiexec=mpiexec)
# 変異位置・種類、iterationを指定
res = ddg.run_batch(m1[0], '1A', ddg_iteration=1)

for x in res:
    ret = (x.basename, x.mutation, str(x.ddg))
    # x.basename: 
    # x.mutation: 
    # x.ddg: ddg score
    # x.mutant_score: mutant rosetta score("ref2015_cart")
    # x.mutant_models: mutant model
    # x.mutant_model.save(pdbfile + '.pdb'): save to pdb file
```


## インストール



[Y-Github]:https://yghe.amzn.ykgw.net/denovo/denovoutil
