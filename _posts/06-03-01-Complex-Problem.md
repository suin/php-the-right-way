---
title: 複雑な問題
isChild: true
anchor: complex_problem
---

## 複雑な問題 {#complex_problem_title}

これまでに依存性の注入について調べたことがある人なら、
*"制御の反転（IoC：Inversion of Control）"* や *"依存関係逆転の原則（DIP：Dependency Inversion Principle）"*
といった言葉に見覚えがあるだろう。これらは複雑な問題で、依存性の注入によって解決できるものだ。

### 制御の反転

制御の反転とは、文字通り、システムの「制御を反転」することだ。システム全体の制御を、オブジェクトから切り離したままで行う。
依存性の注入の文脈では、依存関係の制御や作成を、システム内のどこか別の場所でやるってことを意味する。

PHPのフレームワークでも、制御の反転は実現されてきた。でも、実際のところ、何の制御を反転しているのだろう。そして、反転した結果、制御はどこに行ってしまったのだろう。
たとえば、たいていのMVCフレームワークには、あらゆるコントローラの親になる基底コントローラが用意されている。
そして、それを継承しなければ依存関係のアクセスできない。
これはこれで制御の反転だが、でも、依存関係を緩くしたというよりは、単純に依存関係を別の場所に移しただけのことだ。

依存性の注入を使えば、これをもっとすっきりと解決できる。依存関係が必要になったときに、必要なものだけを注入すればいい。
ハードコーディングする必要はない。

### 依存関係逆転の原則

依存関係逆転の原則は、オブジェクト指向設計の原則である S.O.L.I.D の "D" にあたるもので、
*「抽象に依存しろ。具象に依存するな」* という原則だ。
簡単に言うと、依存関係はインターフェイスや抽象クラスに対して設定すべきものであり、それを実装したクラスに対して設定してはいけないってこと。
先ほどの例をこの原則に沿って書き直すと、こんなふうになる。

{% highlight php %}
<?php
namespace Database;

class Database
{
    protected $adapter;

    public function __construct(AdapterInterface $adapter)
    {
        $this->adapter = $adapter;
    }
}

interface AdapterInterface {}

class MysqlAdapter implements AdapterInterface {}
{% endhighlight %}

これで `Database` クラスは、具象クラスではなくインターフェイスに依存するように変わった。で、いったい何がうれしいんだろう。

こんな場面を考えてみよう。君は今、とあるチームの一員として作業をしている。アダプターを作っているのは別のメンバーだ。
最初の例だと、その人がアダプターを完成させるまでは、自分のコードのユニットテストのモックを作れない。
インターフェイスに依存するようにした新しいバージョンだと、そのインターフェイスを使ってモックを作ることができる。
同僚が作るアダプターもそのインターフェイスに沿っているとわかっているからだ。

そんなことより、もっとすばらしいメリットもある。こっちの方式のほうが、コードがずっとスケーラブルになるんだ。
仮に将来、別のデータベースに移行することになったとしよう。
そんな場合でも、このインターフェイスを実装した新しいアダプターを書いたらそれでおしまいだ。
それ以外は何もいじる必要がない。
新しいアダプターがきちんと決まりごとに従っているということを、インターフェイスが保証してくれるからだ。