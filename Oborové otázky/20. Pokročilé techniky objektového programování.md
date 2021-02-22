# 20.Pokročilé techniky objektového programování

## Agregace

* Mluvíme o agregaci pokud nějaká **třída obsahuje jiné třídy (také se jím říká komponenty)**. 
* Pomocí agregace můžeme tvořit složitější třídy pomocí jednodušších. 
* Konstruktory se v takovém případě chovají tak, že dříve, než se spustí konstruktor třídy, jejíž instanci vytváříme, nejprve se vytvoří instance tříd, které tato třída obsahuje (jejich konstruktory se tedy volají dříve). Volání destruktorů probíhá přesně opačně.

### Příklad

```
#include<iostream>
#define SIZEARRAY 3
using namespace std;

class CA{
  private:
  int m_iNumber;
  
  public:
    CA():m_iNumber(SIZEARRAY)
      { cout << "Konstruktor CA\n";}
    ~CA(){ cout << "Destruktor CA\n";}
    void SetNumber(int a){m_iNumber=a;}
    int GetNumber(void) const {return m_iNumber;}
};

class CB{
  private:
    CA m_CA[SIZEARRAY];
  public:
    CB() { cout << "Konstruktor CB\n";}
    ~CB(){ cout << "Destruktor CB\n";}

    int Suma(void) const{
      int iSuma=0;
      for(int i=0;i<SIZEARRAY;i++)
        iSuma+=m_CA[i].GetNumber();
      
      return iSuma;
};

int main (void)
{
  CB a;
  cout<<"\n "<<a.Suma()<<"\n\n";
  return 0;

}
```

### Vypíše se

```
Konstruktor CA Konstruktor CA Konstruktor CA
Konstruktor CB
9
Destruktor CB
Destruktor CA Destruktor CA Destruktor CA
```

## Spřátelená funkce a spřátelená třída

* Spřátelená funkce není členem třídy, ale má přístup k jejím privátním prvkům.
* **friend** - povoluje přístup metodám a funkcím (spřátelené)
* **publicXfrind** - public zpřístupní datový člen všem funkcím a metodám, frind pouze některým (zvoleným)

### Příklad

```
#include <iostream.h>

class myclass {
  int n, d;
public:
  myclass(int i, int j) { n = i; d = j; }
  friend int isfactor(myclass ob); //spratelena funkce
};

int isfactor(myclass ob)
{
  if(!(ob.n % ob.d)) return 1;
  else return 0;
}

int main()
{
  myclass ob1(10, 2), ob2(13, 3);

  if(isfactor(ob1)) cout << "10 je delitelne 2\n";
  else cout << "10 neni delitelne 2\n";

  if(isfactor(ob2)) cout << "13 je delitelne 3\n";
  else cout << "13 neni delitelne 3\n";

  return 0;
}
```

* Musíme si uvědomit, že spřátelená funkce není členem třídy jejímž je přítelem. Není tedy možné volat spřátelenou funkci s využitím jména objektu a tečkového či šipkového operátoru. Přestože tedy spřátelená funkce zná privátní prvky třídy, s níž je spřátelená, může k nim přistupovat pouze přes objekty třídy.
* Spřátelená funkce není dědičná. Proto, když základní třída obsahuje spřátelenou funkci, pak tato třída není spřátelena s odvozenou funkcí. Dále platí že spřátelená funkce může být spřátelena s více třídami.

## Dědičnost

### Jednoduchá dědičnost

* Základní vztah mezi třídami
* Objekt dědí vlastnosti z jiného objektu a přidává si svoje vlastní
* Vytváření nových tříd (odvozená), které se odvozují od tříd jiných (bázová, rodičovská)
* Jednoduchá dědičnost = třída dědí pouze z jedné třídy

#### Syntaxe
```
class NakladniVozidlo : public Vozidlo{ //Třída NakladniVozidlo dědí z třídy Vozidlo
…
};
```

#### Příklad
```
#include<iostream>

class Vozidlo
{
  private:
    int PocetKol;
  public:
    Vozidlo():PocetKol(0){}
    Vozidlo(int a):PocetKol(a){}
    
    void nastavKola(int a){ PocetKol = a; }
  int dejPocetKol() const { return PocetKol;}
};

class NakladniVozidlo : public Vozidlo
{
private:
  int Nosnost;
public:
  NakladniVozidlo():Vozidlo(0), Nosnost(0){}
  NakladniVozidlo(int a):Vozidlo(a), Nosnost(0){}

  void nastavNosnost(int a){ Nosnost = a; }
int dejNosnost() const { return Nosnost;}
};

int main(void)
{
  Vozidlo Vozidlo1;
  NakladniVozidlo NakladniVozidlo1;
  Vozidlo1.nastavKola(4); 
  NakladniVozidlo1.nastavNosnost(50);

  NakladniVozidlo1.nastavKola(6);

  return 0;
}
```

#### Konstruktory při dědění
* konstruktory i destruktory jsou metody, které se nedědí
* při vytváření instance nějaké třídy se nejprve vyvolá bezparametrický konstruktor nejvyšší nadtřídy, poté jejího potomka, atd.,
* můžeme vyvolat libovolný konstruktor předka v konstruktoru potomka
* Abychom mohli vyvolat konkrétní konstruktor třídy rodičovské, musíme předat příslušný počet parametrů.
* za názvem konstruktoru odvozené třídy se zapíše název rodičovské třídy s danými parametry

```
class NakladniVozidlo : public Vozidlo{
  public:
    NakladniVozidlo (int a) : Vozidlo(a)
    // Konstruktor vyvolává konkrétní konstruktor třídy Vozidlo
    {…/*Tělo konstruktoru*/…}
… /*Tělo třídy*/ …};
```

#### Destruktory při dědění
* stejně jako konstruktory se nedědí
* stejně jako u konstruktorů jsou volány i destruktory předků
* volají se v opačném pořadí

<hr>

### Vícenásobná dědičnost

* nová třída, která dědí z více než jednoho předka
* stejné pravidla jako jednuduchá dědičnost
* vzniká však několik problémů

#### Příklad

```
class potomek : public Predek1, public Predek2 …
{};
```

#### Konstruktory při dědění
* stejně jako u jednoduché dědičnosti, nedědí se
* Pokud nepoužíváme virtuální dědění, nejprve se volá konstruktor nejvyšší nadtřídy
* Pokud bychom využili virtuálního dědění, bude se konstruktor virtuální nadtřídy volat pouze jednou

#### Destruktory při dědění
* chovají se podobně jako konstuktory
* volají se v opačném pořadí

#### Problémy s vícenásobnou dědičností

1. **Konflikt jmen**
  * Jde o problém, jaké atributy a metody dědit, jestliže se vyskytují ve více předcích se stejným názvem
  * třída zdědí všechny atributy a metody i v případě, že mají stejné jméno
  * přistupuje se k položce pomocí tak zvaného úplného jména
```
class C : public A, public B
{ };

int main()
{
  C c;
  c.A::nastav(10);
  c.B::nastav(20);
/* pokud odstraníme následující komentář, překladač
nás chybou upozorní na nejednoznačnost. */
// c.nastav(0);
  return 0;
}
```

2. **Opakovaná dědičnost**
  * mezi dvěmi třídami existuje více než jedna cesta
  * Podle obrázku vlevo třída D obsahuje dva atributy int a.
  * ![alt text](https://github.com/keson3948/Maturita2021_CHC/blob/main/Oborové%20otázky/Obrázky/20_1.png?raw=true)
  * Pokud chceme mít atribut a ve třídě D pouze jednou, musíme třídu A udělat virtuální nadtřídou (u dědění se používá klíčové slovíčko virtual)
```
class A // virtuální nadtřída
{
  int A;
};
class B : virtual public A
{};
class C : virtual public A
{};
class D : public B, public C
{};
```

3. **Konstruktory a destruktory**

## Direktivy public, private, protected

### Public

* přístupné odkludkoliv
* značí veřejné dědění
* nejtypičtěští způsob dědění
* potomek získá věškeré veřejné a chráněné členy rodičovské třídy se stejnými oprávněními, s jakými byly deklarovány v rodičovské třídě

### Private

* proměnná nebo metoda není přístupná nikde jinde než ve třídě, ve které byla definována
* soukromé dědění
* pokud nespecifikujeme přístupový modifikátor, program proměnnou nebo metodu sám definuje na private

### Protected

* k položkám bude mít přístup pouze členové třídy a třídy odvozené (zděděné)
* přístupnost je dána i způsobem dědění

## Způsoby dědění

V jazyce C++ existují tři možnosti, jak dědit. Tyto možnosti se liší v závislosti na tom, zda budou jednotlivé metody a atributy v potomkovi viditelné. Tyto způsoby jsou private, public (nejčastější) a protected.
| | Položka v předkovi je **private**  |Položka v předkovi je **protected**|Položka v předkovi je **public**|
| --- | --- | --- | --- |
| Dědění je private | položka v potomkovi není přístupná | položka v potomkovi je private | položka v potomkovi je private |
| Dědění je protected | položka v potomkovi není přístupná | položka v potomkovi je protected | položka v potomkovi je protected |
| Dědění je public | položka v potomkovi není přístupná | položka v potomkovi je protected | položka v potomkovi je public |

## Polymorfismus
* mnohotvarost
* stejné jméno může mít mnoho forem
* Různé třídy v dědické hierarchii mohou obsahovat metody, které mají stejný název i parametry (pouze návratová hodnota může být jiná)

### Překrývání
* při volání metody se provede ta, která odpovídá instanci příslušné třídy, kterou vytváříme
```
int main()
{
  //Třídy Savec a Pes mají stejnou metodu Zvuk(), ale každá vypíše něco jiného
  Savec Savec1;
  Pes Pes1;

  Savec1.Zvuk();
  // Vypíše se „Zvuk třídy Savec“

  Pes1.Zvuk();
  // Vypíše se „Zvuk třídy Pes“

return 0;
}
```
* rodičovskou metodo můžeme zavolat i z instance jejího potomka, plným uvedením jejího názvu

```
int main()
{
  Pes Pes1;
  //Zavolá se metoda Zvuk u rodičovské třídy Savec
  Pes1.Savec::Zvuk();

  return 0;
}
```

### Virtuální metody
* klíčové slovo **virtual**
* metody, které nechceme definovat (slouží jako šablona)

### Časná a pozdní vazba

#### Časná vazba
*  metoda, která se bude volat, se již rozhodne v době kompilace programu
*  pokud příslušné metody neoznačíme jako virtuální
*  překladač hledá metodu v rodičovské třídě

```
int main()
{
  Savec *Savec1;
  Savec1=new Pes;
  // naprosto korektní, protože Pes je zároveň i Savec
  
  delete Savec1;
  return 0;
}
```

#### Pozdní vazba
*  metoda, která se bude volat, se rozhodne až v době běhu programu
*  dochází to u tzv. virutálních metod
*  Program si vytváří tabulku virtuálních metod (v-tabulka) pro každý objekt daného typu. Tato tabulka obsahuje ukazatele na jednotlivé virtuální funkce
*  z v-tabulky se zjistí, která metoda má být doopravdy volána (adresa je uložena ve v-tabulce)

```
int main()

{
  Savec *Savec1;
  Savec1=new Pes;
  Savec1->Zvuk();
// Vypíše se „Zvuk třídy Pes“
// pokud by metody Zvuk nebyly virtuální, vypsalo
// by se „Zvuk třídy Savec“

  delete Savec1;
  return 0;
}
```

##### Omezení
* virutální metoda nemůže být vložená (inline)
* konstruktor nemůže být virtuální
* pokud třída má v. metodu, destroktur by měl být virtuální (jinak bude volán "nesprávný" destruktor)



### Abstraktní třída
* Třída, která má alespoň jednu čirou metodu
* nemůžeme vytvářet instance

```
class GrafickyUtvar // abstraktní třída
{
  public:
    virtual float dejObvod() = 0; // čirá metoda
};
```

### Čirá metoda
* metoda, která nemá žádné tělo
* nelze ji vyvolat
* může být jen metoda virtuální

```
  virtual float dejObvod() = 0; // čirá metoda
```

### Přetěžování operátorů
* v C++ můžeme operátory přetěžovat, jako obyčejnou funkci (dva parametry, vrací výsledek)
* jednotlivé operátory se musí lišit v typech parametrů
* Pokud přetížíme binární operátor (má dva operandy), musí být opět binární
* Pro operátory platí stejná priorita ať jsou přetížené či nikoliv
* Nelze přetěžovat operátory . .* :: ?:

#### Jak přetěžovat
* Buď jako členské metody tříd nebo jako samostatné funkce
* Binární operátor přetížen jako funkce bude mít dva parametry, unární jen jeden
* Binární operátorpřetížený jako metoda bude mít jen jeden parametr, druhým bude this. Unární operátor přetížený jako členská metoda nebude mít parametr,
protože jeho jediným parametrem je this

#### Prexifové++
* kláčové slovo **operator**
* např. ++i

```
//v metodě dané třídy by mohlo vypadat následovně:

class Citac{
  private:
    int Cislo;
  public:
    Citac():Cislo(0){}
    void operator++() { ++this->Cislo; }
};

void main(){
  Citac i;
  ++i; // proměnná Cislo se v instanci zvýší o 1
}
```

```
//třída bude vypadat takto:

class Citac{
  private: int Cislo;
  public: Citac():Cislo(0){} // konstruktor
    Citac operator++() // funkce přetíženého ++
    {
      Citac pomoc; // pomocná instance pro návrat
      ++Cislo;
      pomoc.NastavHodnotu(Cislo);
      return pomoc;
    }
    void NastavHodnotu(int x){Cislo=x;}
    int DejHodnotu(void) const {return(Cislo);}
};
```

#### Postfixové++
* kláčové slovo **operator**
* např. i++

```
Citac operator++(int) // funkce přetíženého ++ (postfixový)
{
  Citac pomoc; // pomocná instance pro návrat
  pomoc.NastavHodnotu(Cislo);

/* nejprve se v nové instanci nastaví hodnota a teprve potom
se hodnota v původní zvýší o jedničku */

  ++Cislo;
  return pomoc;
}
```

#### Operátor sčítání
* binární (obdobně jako operátory - * /)
* jeden parametr

```
Citac operator+(Citac druhy)
{
  Citac pomoc;
  pomoc.NastavHodnotu(Cislo+druhy.DejHodnotu());
  
  return(pomoc);
}

//Mimo třídu můžeme např. použít:

Citac a, b;
Citac c=a+b;
```

#### Operátor porovnání
* můžeme porovnávat dva objekty
* To, zda jsou dvě instance stejné, zjišťujeme podle rovnosti atributů
```
bool operator==(Citac druhy)
{
  if(Cislo==druhy.DejHodnotu())
    return true; // atributy jsou stejné
  else
    return false; // atributy nejsou stejné
}

//Mimo třídu můžeme např. použít:

Citac a, b;
if (a==b)
```
