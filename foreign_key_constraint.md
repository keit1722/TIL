# 外部キー制約

## 外部キー制約とは

関連付ける2つのテーブルの間でデータの整合性を保つための制約（約束事）。制約として以下の2つがある。

- 親テーブルの指定したデータ以外は、子テーブルの外部キーとして登録できない
- 親テーブルのデータを削除しようとしたときに、そのデータが子テーブルに外部キーとして使われている場合は親テーブルのレコードを削除できない

メリット
- データの整合性を保てる
- 誤って親テーブルのデータを消すことを防げる

## 外部キー制約のついたカラムの作成方法

テーブル作成時にreference型で `foreign_key: ture` を付けてカラムを作成

マイグレーションファイルを作成して以下のように記述
（例：user:task の関係が 1:N ）

```
  def change
    create_table :tasks do |t|
      t.string    :name
      t.text      :description
      t.references :user, foreign_key: true
    end
  end
```

この記述でTaskテーブルには以下のカラムが作成される
- name
- description
- user_id → 外部キー制約が付いている

外部キー制約の付いたカラムを追加したい場合は、マイグレーションファイルを作成して以下を記述

```
def change
  add_reference :tasks, :user, foreign_key: true
end
```

## 注意すること

メリットでもあるが、外部キー制約を付けると子テーブルが外部キーとして参照している親テーブルのレコードは削除できない

解決方法としては、親テーブルのモデルに `dependent` オプションで`:destroy`を指定すれば良い。

```
class User < ApplicationRecord
  has_many :tasks, dependent: :destroy
end   
```

この場合、親テーブルを削除すると関連付けられた子テーブルも全て削除される。