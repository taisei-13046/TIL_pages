---
layout: post
title: "modelについて学習" 
date: 2021-12-25 
category: read 
excerpt: ""
---

# やったこと
Modelの操作をいまいち理解していない気がするので、改めて振り返りとしてまとめてみる。  
ひたすらドキュメントを読む [Django Doc Model](https://docs.djangoproject.com/ja/4.0/topics/db/models/)
## Modelについて

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)
```
このモデルを定義した場合は、
```python
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```
このようなデータベースのテーブルを作成する。 (SQLがさっぱりなのでまた読まないとな。。。)


## フィールドオプション
1. **null**: True の場合、Django はデータベース内に NULL として空の値を保持する
2. **blank**: True の場合、フィールドはブランクになることが許容される
3. **choices**: デフォルトのフォームウィジェットが標準のテキストボックスではなくセレクトボックスになり、与えられた選択肢を選ぶように制限される
4. **default**: そのフィールドのデフォルト値。
5. **primary_key**: True の場合、設定したフィールドはそのモデルの主キーとなる。設定されなかったら、自動的に設定される。
6. **unique**: True の場合、そのフィールドはテーブル上で一意となる制約を受ける。


### null と blankの違い  
- blank がバリデーション由来である一方、 null は完全にデータベース由来である。
- blankはDjangoのフォームからの投稿が空かどうかを判定するもの、nullはデータベースの中身が空かどうかを判定するもの  

この部分大切そうだからもう少し深ぼる。。。  
null と blankの組み合わせは以下の4パターンがある  
- **blank=False, null=Falseのパターン**
    - フォームからの投稿時にこのフィールドの入力は必須、データベースに保存される値も空になってはいけない。
- **blank=True, null=Trueのパターン**
    - フォームから投稿するときにこのフィールドの入力は必須ではない、データベースに保存される値になにも入っていなくても大丈夫
- **blank=True, null=Falseのパターン**
    - フォームから投稿するときにはこのフィールドの入力は必須ではない、しかし、データベースに保存される値が空になってはいけない。
    - これが一番意味がわからないが、CharFieldやTextFieldのような文字列値においては、空の値はnullではなくて空文字列として保存されるため、文字列を扱うフィールドに限っては、blank=Trueのみの指定を許すことができる
- **blank=False, null=Trueのパターン**
    - フォームからの入力時には必ずこのフィールドを入力する必要がある、しかし、データベースの値は空であっても構わない
    - これは使う機会が少ない  
このようにnullとblankの組み合わせを場合分けしてみると、お互いの違いがよくわかる。  
参考資料[nullとblankの違いについて](https://djangobrothers.com/blogs/django_null_blank/#blankFalsenullTrue)

### choicesについて
- フィールドを選択肢として使用するには、2タプルの sequence を使用
```python
YEAR_IN_SCHOOL_CHOICES = [
    ('FR', 'Freshman'),
    ('SO', 'Sophomore'),
    ('JR', 'Junior'),
    ('SR', 'Senior'),
    ('GR', 'Graduate'),
]
```
- choices の順番を変更すると、変更のたびに新しいマイグレーションが生成される  

### 詳細な (verbose) フィールド名
```python
first_name = models.CharField("person's first name", max_length=30)
```
上のように第一引数にverbose_nameを指定できる。  
verbose_nameとは、**管理画面でのモデルの名前を指定することができるもの**であり、指定しないとモデル名を解体した文字列がそのまま管理画面に表示される。

## リレーション 
### 多対一 (many-to-one) 関係
ForignKeyを使用する
```python
class Car(models.Model):
    company_that_makes_it = models.ForeignKey(
        Manufacturer,
        on_delete=models.CASCADE,
    )
    # ...
```
- フィールド名はモデル名を小文字にしたものをおすすめ

### 多対多 (many-to-many) 関係
```python
from django.db import models

class Topping(models.Model):
    # ...
    pass

class Pizza(models.Model):
    # ...
    toppings = models.ManyToManyField(Topping)
```
- 命名は関係モデルオブジェクトの複数形が推奨
- ManyToManyField インスタンスは、フォームで編集されるオブジェクトの側にある
- 上の例では、 toppings は Pizza の中にあります  
    ( Topping が pizzas の ManyToManyField を持つとするよりはそのほうがいいでしょう ) 。  
    というのは、ピザが複数のトッピングを持つほうが、トッピングが複数のピザの上にあるというよりも自然だからです。  
    このようにすることで、ユーザは `` Pizza`` フォームでトッピングを選ぶことができるようになります。  

### 多対多リレーションにおける追加フィールド
ピザとトッピングを組み合わせる程度の多対多リレーションを扱うのであれば、標準の ManyToManyField で十分。  
しかし、2 つのモデルのリレーションに他のデータを付加したくなることもある。  
このような場合、 Django ではそのような多対多リレーションを規定するのに使われるモデルを指定することができる。  
そうすることで、中間モデルに追加のフィールドを配置することができます。  
中間モデルは、 through 引数で中間として振る舞うモデルを指定することで、 ManyToManyField に紐付けることができる。  
```python
from django.db import models

class Person(models.Model):
    name = models.CharField(max_length=128)

    def __str__(self):
        return self.name

class Group(models.Model):
    name = models.CharField(max_length=128)
    members = models.ManyToManyField(Person, through='Membership')

    def __str__(self):
        return self.name

class Membership(models.Model):
    person = models.ForeignKey(Person, on_delete=models.CASCADE)
    group = models.ForeignKey(Group, on_delete=models.CASCADE)
    date_joined = models.DateField()
    invite_reason = models.CharField(max_length=64)
```
### 一対一 (one-to-one) 関係
OneToOneFieldを使用する
[一対一モデル使用例](https://docs.djangoproject.com/ja/4.0/topics/db/examples/one_to_one/)

## モデルの継承
Djangoにおいて可能な継承には3つの方式がある
1. 子モデルそれぞれに対して一々定義し直さないで済ませたい情報を保持するためだけに、親クラスを使用することがあるかもしれません。  
    このクラスが単体で用いられることはないので、この場合 **抽象基底クラス** が適切です。
2. もし既存のモデル (完全に別のアプリケーション等から取得した) のサブクラスを作成していてモデルそれぞれにデータベース上のテーブルを定義したい場合、   
    **複数テーブルの継承** を利用するとよいでしょう。
3. 最後に、モデルの Python レベルでの振る舞いを、モデルのフィールドを変更せずに修正したい場合は、 **プロキシモデル** を利用できます。

### 抽象基底クラス
抽象基底クラスは、複数の他モデルに対して共通の情報を入れ込みたいときに有用。  
```python
from django.db import models

class CommonInfo(models.Model):
    name = models.CharField(max_length=100)
    age = models.PositiveIntegerField()

    class Meta:
        abstract = True

class Student(CommonInfo):
    home_group = models.CharField(max_length=5)
```
基底クラスを書いて Meta クラス内で abstract=True をセットする。  
これで、このモデルはデータベーステーブルを作成するために使用されることはなくなる。  
代わりに、他のモデルで基底クラスとして使われる際に、これら子クラスのフィールドとして追加される。

```python
from django.db import models

class CommonInfo(models.Model):
    # ...
    class Meta:
        abstract = True
        ordering = ['name']

class Student(CommonInfo):
    # ...
    class Meta(CommonInfo.Meta):
        db_table = 'student_info'
```
抽象基底クラスを作成した際、Django は基底クラスで宣言したすべての Meta インナークラスを子クラスの属性とします。  
子クラスが自身の Meta クラスを定義しなければ、親の Meta クラスを継承します。  
子クラスが親の Meta クラスを拡張したい場合、サブクラス化できます。  

### related_name と related_query_name の利用に関して注意するべき点
ForeignKey もしくは ManyToManyField に対して related_name または related_query_name を使う場合、  
そのフィールドに対して 一意の 逆引き名およびクエリ名を常に定義しなければいけない。  
```python
from django.db import models

class Base(models.Model):
    m2m = models.ManyToManyField(
        OtherModel,
        related_name="%(app_label)s_%(class)s_related",
        related_query_name="%(app_label)s_%(class)ss",
    )

    class Meta:
        abstract = True

class ChildA(Base):
    pass

class ChildB(Base):
    pass
```
この問題には上記のように対応する。
- **'%(class)s'** は、フィールドが使用されている子クラスの名前を小文字にした文字列と置換される。
- **'%(app_label)s'** は、子クラスが含まれているアプリ名を小文字にした文字列と置換される。

### 複数テーブルの継承
Django がサポートするもう一つのモデル継承は、ヒエラルキー内の各モデルすべてが、それ自体モデルであるような場合。  
それぞれのモデルはデータベース上のテーブルに対応しており、個別にクエリーを作成したり、テーブルの作成ができる。  
```python
from django.db import models

class Place(models.Model):
    name = models.CharField(max_length=50)
    address = models.CharField(max_length=80)

class Restaurant(Place):
    serves_hot_dogs = models.BooleanField(default=False)
    serves_pizza = models.BooleanField(default=False)
```
- PlaceのフィールドはRestaurantでも利用可能
- もし、Restaurant でもある Place が存在する時、小文字にしたモデル名を使うことで、Place オブジェクトから Restaurant オブジェクトを取得できる。
```python
>>> p = Place.objects.get(id=12)
# If p is a Restaurant object, this will give the child class:
>>> p.restaurant
<Restaurant: ...>
```
しかし、上の例において p が Restaurant オブジェクト ではない 場合 (つまり、継承を用いず Place オブジェクトが直接作成されたもしくは他のクラスの親であった場合)、  
p.restaurant を参照すると、Restaurant.DoesNotExist 例外が発生する

### プロキシモデル
モデルの Python 上の振る舞いを変更したい事も有る  
デフォルトのマネージャーを変更したり、新たなメソッドを追加したりする場合

```python
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)

class MyPerson(Person):
    class Meta:
        proxy = True

    def do_something(self):
        # ...
        pass
```
**proxy = True**でプロキシモデルを作成できる  

プロキシモデル上にモデルのマネージャーを定義しない場合、そのプロキシモデルはマネージャーを親のモデルから継承します。プロキシモデル上にマネージャーを定義する場合、親クラス上のマネージャーで定義されて利用可能な物が有ったとしても、そのマネージャーがデフォルトになります。
```python
from django.db import models

class NewManager(models.Manager):
    # ...
    pass

class MyPerson(Person):
    objects = NewManager()

    class Meta:
        proxy = True
```

## パッケージ化したモデルを扱う
多数のモデルが存在する場合、それらを別のファイルとして管理するのが良い時もある。  
その場合は、modelsパッケージを作成してmyapp/models/ディレクトリに__init__.pyを設置する
```python
from .organic import Person
from .synthetic import Robot
```

## 次回の予定
今回はDjango documentのModelの章を読んだ。  
次はクエリ操作について熟読したい。
