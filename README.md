# XML-RDF Convertor 1.0

XML Schema(XSD)として定義されるメタデータスキーマを、OWL形式のスキーマに変換するツールです。
本ツールはPythonで開発されており、実行ファイルは`xml_rdf_convertor.py`です。
変換に必要な情報はルールファイルにて記述します。このルールファイルはプログラムにより生成することができ、それをユーザが編集することで最適化することができます。

## 必要環境

- Python 3.10 以上推奨
- 追加ライブラリ:
  - `rdflib`

インストール例:

```powershell
pip install rdflib
```

## 機能

`xml_rdf_convertor.py` は 2 つの機能を有しています。

- `schema`: XSD → OWL(Turtle)
- `instance`: XML(インスタンス) → RDF(Turtle)

## 使い方

### 1. XSD → OWL(Turtle)

```powershell
python xml_rdf_convertor.py schema your_schema.xsd --rules schema_rules.tsv -o schema.owl.ttl
```

ルールファイルのひな型を新規作成したい場合:

```powershell
python xml_rdf_convertor.py schema --init-rules schema_rules.tsv
```

### 2. XML(インスタンス) → RDF(Turtle)

```powershell
python xml_rdf_convertor.py instance your_schema.xsd your_instance.xml --rules instance_rules.tsv -o instances.ttl
```

ルールファイルのひな型を新規作成したい場合:

```powershell
python xml_rdf_convertor.py instance --init-rules instance_rules.tsv
```

### 3. 本プログラムのバージョン確認方法

```powershell
python xml_rdf_convertor.py --version
```

## 同梱ファイル

### ルールファイルの雛形

- `schema_default_rules.tsv`
- `schema_default_rules.json`
- `instance_default_rules.tsv`
- `instance_default_rules.json`

### OME サンプル

OME（**Open Microscopy Environment**）は、生物顕微鏡データの保存・操作のためのオープンソースソフトウェアとデータ形式標準を開発するプロジェクトです。

- 公式サイト: <https://www.openmicroscopy.org/>
- OME-XML の説明: <https://docs.openmicroscopy.org/ome-model/latest/ome-xml/>
- OME スキーマ一覧: <https://www.openmicroscopy.org/Schemas/OME/>
- OME 2016-06 schema (ome.xsd): <http://www.openmicroscopy.org/Schemas/OME/2016-06/ome.xsd>

#### OME サンプル設定

- `examples/ome/ome_schema_rules.tsv`
  - OME の XSD → OWL 用サンプル設定
  - 検証 (`verify`) はポータブル性のため無効化しています
- `examples/ome/ome_instance_rules.tsv`
  - OME の XML(インスタンス) → RDF 用サンプル設定
- `examples/ome/ome_sample.xml`
  - インスタンス変換の最小サンプル

##### OME の実行例

```powershell
python xml_rdf_convertor.py schema ome.xsd --rules examples/ome/ome_schema_rules.tsv -o ome.owl.ttl
```

```powershell
python xml_rdf_convertor.py instance ome.xsd examples/ome/ome_sample.xml --rules examples/ome/ome_instance_rules.tsv -o ome_sample_instances.ttl
```

### SPASE サンプル

SPASE（**Space Physics Archive Search and Extract**）は、**ヘリオフィジクス（地球・宇宙空間物理）資源**を一様に記述するためのメタデータ標準／データモデルです。公式サイトでは、Observatory、Instrument、Repository、Numerical Data などの資源を統一的に記述するための語彙とXMLスキーマを提供しています。

- 公式サイト: <https://www.spase-group.org/>
- SPASE Base Model / Human-readable model docs: <https://spase-group.org/data/model/index.html>
- SPASE XML Schema index: <https://spase-group.org/data/schema/index.html>
- SPASE 2.6.1 schema: <https://spase-group.org/data/schema/spase-2.6.1.xsd>
- 
#### SPASE サンプル設定

- `examples/spase/spase_schema_rules.tsv`
  - SPASE 用の基本設定
  - `measurement.numeric_default=datatype`

##### SPASE の実行例

```powershell
python xml_rdf_convertor.py schema spase-2.6.1.xsd --rules examples/spase/spase_schema_rules.tsv -o spase.owl.ttl
```

```powershell
python xml_rdf_convertor.py schema spase-2.6.1.xsd --rules examples/spase/spase_schema_rules_measure_object.tsv -o spase_measure_object.owl.ttl
```

## ルールファイルについて

- JSON と TSV の両形式の扱いが可能です。
- TSV は `key<TAB>value` 形式です
- 配列は `key[]`
- 辞書は `key[subkey]`

例:

```tsv
project.base_ns	http://example.org/ontology/
naming.lower_first_properties	true
measurement.unitless_literal_properties[]	sizeX
measurement.unitless_literal_properties[]	sizeY
```

## ルールファイル編集の注意事項

- `project.base_ns` は実質必須です。未指定の場合は警告を出し、仮の名前空間で続行します（strict モードでは停止）。
- `measurement.numeric_default=object` のとき、単位ペア (`*Unit`) が見つからない数値項目は警告対象になります。
- 必要に応じて `validation.todo_patch_path` を設定すると、追記候補のルールを TSV / JSON で出力できます。

## 謝辞

本ツールの開発の一部は、「AI等の活用を推進する研究データエコシステム構築事業」、理化学研究所TRIP事業にて実施しました。
