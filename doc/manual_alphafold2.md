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
- step5: 上部タブの"Runtime"->"Run all"で、計算を実行する。（30配列について、約10分で計算が完了した）
- step6: 結果が描画される。
    - global pLDDTが5位までの予測構造が出力される。
    - Display 3D structure: 予測構造が描画される。
    - model quality, MSA coverage
- step7: zipファイルのダウンロードのポップアップが出現する。"save_to_google_drive"にチェックを入れていた場合、zipファイルがdriveに保存される。

## 出力ファイルの構造
- "[job_name].result"というzipファイルとして保存される。
- config.json: configファイル
- XX_coverage.png,XX_PAE.png,XX_plddt.png: 画像ファイル
- XX_predicted_alinged_error_v1.json: pAE
- XX_rank_1_model_[num].pdb, XX_rank_2_model_[num].pdb, XX_rank_3_model_[num].pdb, XX_rank_4_model_[num].pdb, XX_rank_5_model_[num].pdb: 1~5位までの予測構造のpdbデータ（全原子の位置座標、plddtスコア）
- XX_rank_1_model_[num]_scores.json, XX_rank_2_model_[num]_scores.json, XX_rank_3_model_[num]_scores.json, XX_rank_4_model_[num]_scores.json, XX_rank_5_model_[num]_scores.json: 各残基のpAE
- その他は重要でない

## pymolによる描画
- TBD


[af2anatomia]:https://www.af2anatomia.jp/
[colabfold_af2]:https://colab.research.google.com/github/sokrypton/ColabFold/blob/main/AlphaFold2.ipynb
[num_recycles]:https://www.af2anatomia.jp/Supplementary/1.10%20Recycling%20iterations
