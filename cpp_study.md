関数
===============================
要素
- 関数オーバーロード
- 関数テンプレート


関数宣言
--------------------------------
関数のプロトタイプ宣言、もしくは本体を呼び出す前の位置に見えるようにしておく必要がある。


関数オーバーロード
--------------------------------
他言語のメソッドオーバーロードみたいに同じ名前の関数で複数定義が可能になった。
````
#include <cmath>
#include <iostream>


int main()
{
  const float f1 = std::fabs(-10.5f); //引数がfloatなら戻り地もfloat
  const double f2 = std::fabs(-10.5);
  const long double f3 = std::fabs(-10.5L); //引数がlong doubleになる
}
````
関数テンプレート
--------------------------------
宣言の方法は下記の通り
````
template <typename テンプレートパラメータ名> 戻り値型 関数テンプレート名(仮引数の並び)
````
使用例を記載します。
````
#include <cstdio>
template <typename T> T change_sign(T a);  //宣言 どういうときに使うかは不明
template <typename Ret, typename T> Ret cast(T a);  
//↑ これをヘッダに書いたときは実装もヘッダに書くらしい。




//実引数の符号を反転させた値を返す。
template <typename T>
T change_sign(T a)
{
  return -a;
}


//実引数をRetの型にキャストする。
template <typename Ret, typename T>
Ret cast(T a)
{
  return static_cast<Ret>(a);
}


int main()
{
  std::printf("%d\n", change_sign(10))      // -10
  std::printf("%d\n", change_sign(-12.5))   // 12.5
}
//
````


### テンプレート実引数を明示する
上の例では実引数から暗黙的に当てはめられていたが、明示的に指定することも可能
````
//実引数をRetの型にキャストする。
#include <cstdio>
template <typename Ret, typename T>
Ret cast(T a)
{
  return static_cast<Ret>(a);
}


int main()
{
  const double a= 12.5;
  const int b = cast<int, double>(a);  // < 形を指定する
  const int c = cast<int>(a);  //2個めを省略した 1個めだけを明示
}
````
### 関数テンプレートを定義する位置
関数テンプレートの宣言をヘッダファイルに記述する場合には定義もヘッダファイルに記述する必要があります。


入出力機能
-------------------------------------------
C++では iostream という入出力が使えます。 stdio.hも cstdio という名前になっていますが、使用可能です。
### 標準出力ストリーム
````
#include <iostream>
int main()
{
  const int a = 10;
  const double b = 5.5;
  const char* const c = "xyz";


  std::cout << a << "\n"
            << v << "\n"
            << c << std::endl;
}
````
std::cout が標準出力。それにシフト演算子(右)"<<"を使って指示する
std::endlは「改行文字 + バッファのフラッシュ」という役割がある。


### 標準エラーストリーム
std::cout の代わりに　std::err を使用すればいい


### 標準入力ストリーム
標準入力は std::cin にたいして>>演算子を用いる
````
#include <iostream>
int main()
{
  int a;
  double b;
  char c[80];
  std::cin >> a >> b >> c; // "5 5.5 abc"と入力するとそれぞれa: 5, b:5.5, c=abcが入る  
}
````

うえではchar型で受け取ったが、Buffer OverFlowが発生する可能性があるため、std::string型を使ったほうが良い。
入力が想定しているものと違う型だと、エラーが発生してしまう。
そちらの対応は下記のようにスル。
````````````````````````````````````````````
#include <iostream>
int main()
{
  int a;
  std::change_sign >> a;
  if (std::cin) //正常な状態か？
  {
    std::cout << a << std::endl;
  }else{
    std::cout << "error" <<std::endl;
  }
}
````````````````````````````````````````````
インライン関数
--------------------------------------------
関数呼び出しの負荷を低減したい。Cのときは関数形式マクロ。それに対応するもので、C++の場合はインライン関数を使用する。
````````````````````````````````````````````

inline int cube(int a) //先頭に inline と埋め込む
{
  return a * a * a;
}

int main()
{
  const int x = cube(5); //ここにコードが埋め込まれる
}
````````````````````````````````````````````
inline展開が行われるには、インライン関数を呼び出そうとしている箇所からインライン関数の定義が見えている必要がある。
インライン関数をヘッダに入れて共有したいなら、定義もすべてヘッダに記入する必要がある。  
また、inline指定したとしてもコンパイラがインライン展開するかどうかは不明。最適化を通じて判断される。

関数形式マクロを避ける
-------------------------------------------------------
大概の場合、関数形式マクロは関数テンプレートとinlineキーワードの組み合わせを使って置き換えることができる...らしい。

````````````````````````````````````````````
#include <iostream>

template <typename T>
inline T cube(T a)
{
  return a*b*c;
}

int main ()
{
  const int x1 = cube(5);
  const double x2 = cube(1.1)
}
`````````````````````````````````````````````
クラス
============================================
とりあえず、まずは構造体レベルのもの。
``````````````````````````````````
//クラスの作り方 最初の一歩
class Point
{
    int x,y; //メンバ変数
}

Point point;
``````````````````````````````````

メンバ変数は公開すべきではないので、
アクセス用の関数を用意する

```````````````````````````````````
#include <iostream>

class Point
{
public: //アクセス関数？アクセサ？メンバ関数？は公開
  void SetXY(int x, int y);
  int GetX();
  int GetY();

private://メンバは非公開
  int x,y;
};

void Point::SetXY(int x, int y)
{
  this->x = x; //メンバ x だけだと被るから、this->x  yも同じ
  this->y = y; //めんどくさければyをもっと個性的に、 例えば y -> m_y とか
}

int Point::GetX()
{
  return this->x;
}

int Point::GetY()
{
  return this->y;  
}

int main()
{
  Point point;
  point.SetXY(3,5);
  std::cout <<point.GetX <<", "<<point.GetY<<std::endl;
}
```````````````````````````````````

サイズが大きくなってきたら、クラス定義(class{}の部分)はヘッダファイルに、
関数定義は対応するソースファイルにそれぞれ追加する。

ごく単純なメンバ関数なら宣言と定義を合体させてもいい
inline関数と同じ扱いになる。

`````````````````````````````````````````````
//Point.h
#ifndef POINT_H_INCLUDE
#define POINT_H_INCLUDE

class Point
{
public:
  void SetXY(int x, int y)  //<-inline扱いになる
  {
    mX = x;
    mY = y;
  }

  int GetX() //<-inline扱いになる
  {
    return mX;
  }

  int GetY() //<-inline扱いになる
  {
    return mY;
  }

private:
  int mX;
  int mYl;
};
#endif
`````````````````````````````````````````````
