# AlphaFold2
- 本書は、_De Novo_ Pjtのために、AlphaFold2、特にColabFoldのマニュアルをまとめたものである。

## AlphaFold2の背景
- AlphaFold2（AF2）は、Alphabet(Google) DeepMindが開発したタンパク質立体構造予測技術、およびツールである。
- タンパク質構造予測は、アミノ酸配列の機能を解明することに繋がるため、創薬や産業などで重要な問題とされていた。
- CASP（Critical Assessment of protein Structure Prediction）と呼ばれるコンペティションが開催されており、2018年にAF1、2020年にAF2が優勝した。
- 特に、AF2は2位のBakerラボグループのスコアと圧倒的な差をつけ、Cα原子の精度は1サブÅであった。

## AlphaFold2の種類
- colabfold
    - colabfoldは、GoogleColaboratory上で計算を実行できるため、ローカルPCへのインストールが不要。

## ColabFoldの使い方
- 詳細は[alphafold2解体新書][af2anatomia]を参照されたい。
- step1: [GoogleColaboratory][colabfold_af2]（単量体、MMseqs2）を開く。
- step2: Input protein sequenceを設定する。
    - query_sequence: 構造予測したいアミノ酸配列。
    - jobname: job名。空だと、ランダムな文字列が割り当てられる。（単純に実行毎の結果を区別するための名称）
    - use_amber: amberによるrelaxを使用する？（正確な意味が判明していないが、チェックしなくても動作する）
    - use_templete: テンプレートを使用する場合はpdb名を入力。使用しない場合は"none"を入力。
- step3: 必要であれば、MSA_optionsを入力する。（基本的にはデフォルトでOK）
- step4: Advanced_settingsを設定する。
    - model_type: AFのモデル。"auto"であれば、適切なモデルを使用する。
    - num_recycles: ネットワークの実行回数。[num_recycles解説][num_recycles]を参照。（基本的にはデフォルトでOK）
    - save_to_google_drive: 結果ファイル一式をzipとしてgoogledriveに保存する。（デフォルトはチェックが入っていないため注意）
    - dpi: 保存画像の解像度。（基本的にはデフォルトの200でOK）
- step5: 上部タブの"Runtime"->"Run all"で、計算を実行する。（37配列について、約10分で計算が完了した）
- step6: 結果が描画される。
    - global pLDDTが5位までの予測構造が出力される。
    - Display 3D structure: 予測構造が描画される。
    - model quality, MSA coverage
- step7: zipファイルのダウンロードのポップアップが出現する。"save_to_google_drive"にチェックを入れていた場合、zipファイルがdriveに保存される。

## 出力ファイルの構造
- "[job_name].result"というzipファイルとして保存される。
- config.json: configファイル
- XX_coverage.png, XX_PAE.png, XX_plddt.png: 画像ファイル
- XX_predicted_alinged_error_v1.json: pAE
- XX_rank_1_model_[num].pdb, XX_rank_2_model_[num].pdb, XX_rank_3_model_[num].pdb, XX_rank_4_model_[num].pdb, XX_rank_5_model_[num].pdb: 1~5位までの予測構造のpdbデータ（全原子の位置座標、plddtスコア）
- XX_rank_1_model_[num]_scores.json, XX_rank_2_model_[num]_scores.json, XX_rank_3_model_[num]_scores.json, XX_rank_4_model_[num]_scores.json, XX_rank_5_model_[num]_scores.json: 各残基のpAE
- その他は重要でない

## pymolによる描画
- 大量の変異体について実行すると、上記のzipを獲得しても、スコアを集約するのが大変だったり、各構造ファイルについて図を統一できない。
- このため、colabfoldの出力ファイルについて、これらの作業を簡易的に実行するスクリプトを作成した。

### スコア集約
- get_score.pyがスコア集約のスクリプト。
- step1: config_get_score.iniから、各構造ファイルを集約している親フォルダ名（parent）を指定する。
    - 下記のように、ディレクトリ構造を整理する。
        - work_dir/parent/each_job/job_name.result.zip
- step2: get_score.pyを実行する。
    - parent以下の全てのフォルダ名を取得し、各jobに対してスコアを取得・集約する。
    - zipファイルを解凍していない場合、自動的に解凍してjob_name.resultディレクトリを生成する。解凍済なら、そのディレクトリを参照する。
- step3: 結果が出力される。
    - 各jobフォルダ内でoutputフォルダを作成し、そこに下記スコアのデータフレーム（score.csv）を保存する。
        - clm = ['max_pae', 'ptm', 'pLDDT_mean', 'pAC', 'model_num', 'rank']
    - 親フォルダ内でoutputフォルダを作成し、そこに各jobのスコアファイルを結合したデータフレーム（score_AF.csv）を保存する。
        - clm = ['sample_no', 'max_pae', 'ptm', 'pLDDT_mean', 'pAC', 'model_num', 'rank', 'parent']

### 参照pdbのコピー
- copy_refer_pdb.pyが参照pdbコピースクリプト。
- step1: config_copy_refer_pdb.iniから、各構造ファイルを集約している親フォルダ名（parent）とpdbデータのコピー元フォルダを指定する。
    - 下記のように、ディレクトリ構造を整理する。
    - work_dir/parent/each_job/job_name.result
    - enznasにpdbデータを集めていることが多いため、コピー元はenznasに設定する。
    - ただし、コピー元のdescriptionリストは、現在は既存のスコアファイルから取得している。コピー元のフォルダ内をすべて検索するほうが良いかも。
- step2: copy_refer_pdb.pyを実行する。
    - 各jobフォルダ内でreferフォルダを作成し、そこにcopy元から該当するpdbデータをコピーする。


[af2anatomia]:https://www.af2anatomia.jp/
[colabfold_af2]:https://colab.research.google.com/github/sokrypton/ColabFold/blob/main/AlphaFold2.ipynb
[num_recycles]:https://www.af2anatomia.jp/Supplementary/1.10%20Recycling%20iterations
