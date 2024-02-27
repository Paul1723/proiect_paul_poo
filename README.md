#define _CRT_SECURE_NO_WARNINGS
#include <iostream>
#include <crtdbg.h>
#include<fstream>

using namespace std;


//Întreținere asociație proprietari
class Locatar

{
private:
	char* nume;
	int nr_apartament;
	string luna;
	float suma_plata;

public:

	//constructor fara parametri
	Locatar()
	{
		this->nume = new char[strlen("nedefinita") + 1];
		strcpy(nume, "nedefinita");
		this->nr_apartament = 0;
		this->luna = "-";
		this->suma_plata = 0;
	}

	//constructor cu parametri
	Locatar(const char* anume, int anr_apartament, string aluna, float asuma_plata)
	{
		this->nume = new char[strlen(anume) + 1];
		strcpy(this->nume, anume);
		this->nr_apartament = anr_apartament;
		this->luna = aluna;
		this->suma_plata = asuma_plata;
	}

	~Locatar()
	{
		delete[] nume;
	}

	//constructor copiere

	Locatar(Locatar const& sursa)
	{
		this->nume = new char[strlen(sursa.nume) + 1];
		strcpy(nume, sursa.nume);
		this->nr_apartament = sursa.nr_apartament;
		this->luna = sursa.luna;
		this->suma_plata = sursa.suma_plata;
	}

	//operator atribuire
	Locatar& operator=(const Locatar& sursa)
	{
		delete[] nume;

		if (this != &sursa)
		{
			this->nume = new char[strlen(sursa.nume) + 1];
			strcpy(nume, sursa.nume);
			this->nr_apartament = sursa.nr_apartament;
			this->luna = sursa.luna;
			this->suma_plata = sursa.suma_plata;
		}
		return *this;
	}

	//operator intrare
	friend ostream& operator<<(ostream& out, Locatar sursa)
	{
		out << sursa.nume << endl;
		out << sursa.nr_apartament << endl;
		out << sursa.luna << endl;
		out << sursa.suma_plata << endl;
		return out;
	}


	//operator iesire
	friend istream& operator>>(istream& in, Locatar sursa)
	{
		cout << "Afiseaza numele locatarului:";
		in >> sursa.nume;
		cout << "Afiseaza apartamentul:";
		in >> sursa.nr_apartament;
		cout << "Afiseaza luna de plata:";
		in >> sursa.luna;
		cout << "Afiseaza suma de plata:";
		in >> sursa.suma_plata;

		return in;
	}


	// Operator mai mic (<)
	friend int operator<(const Locatar& sursa1, const Locatar& sursa2)
	{
		if (sursa1.suma_plata < sursa2.suma_plata)
			return 1;
		else
			return 0;
	}
	// Operator mai mare (>)
	friend int operator>(const Locatar& sursa1, const Locatar& sursa2)
	{
		if (sursa1.suma_plata > sursa2.suma_plata)
			return 1;
		else
			return 0;
	}
	// Operator mai mic sau egal (<=)
	friend int operator<=(const Locatar& sursa1, const Locatar& sursa2)
	{
		if (sursa1.suma_plata <= sursa2.suma_plata)
			return 1;
		else
			return 0;
	}
	// Operator mai mare sau egal (>=)
	friend int operator>=(const Locatar& sursa1, const Locatar& sursa2)
	{
		if (sursa1.suma_plata >= sursa2.suma_plata)
			return 1;
		else
			return 0;
	}

	// Funcție de conversie către float (suma de plată)
	operator float() const
	{
		return suma_plata;
	}

	//Metode specifice


	//Metoda pentru afișarea detaliilor locatarului:

	void afisareDetalii() const
	{
		cout << "Nume: " << nume << endl;
		cout << "Numar apartament: " << nr_apartament << endl;
		cout << "Luna plata: " << luna << endl;
		cout << "Suma plata: " << suma_plata << endl;
	}

	void actualizareNume(const char* noulNume)
	{
		delete[] nume;
		nume = new char[strlen(noulNume) + 1];
		strcpy(nume, noulNume);
	}

	float calculeazaTaxaSuplimentara(float procentTaxa) const
	{
		return suma_plata * (procentTaxa / 100);
	}


	// Get pentru 'nume'
	const char* getNume() const {
		return nume;
	}

	// Getter for 'nr_apartament'
	int getNrApartament() const {
		return nr_apartament;
	}

	// Get pentru 'luna'
	const string& getLuna() const {
		return luna;
	}

	// Get pentru 'suma_plata'
	float getSumaPlata() const {
		return suma_plata;
	}

	//Operator de adunare a sumei de plată cu o valoare dată(+):
	Locatar operator+(float valoare) const
	{
		Locatar rezultat(*this);
		rezultat.suma_plata += valoare;
		return rezultat;
	}

	//Operator de scădere a sumei de plată cu o valoare dată (-):
	Locatar operator-(float valoare) const
	{
		Locatar rezultat(*this);
		rezultat.suma_plata -= valoare;
		return rezultat;
	}
	//Operator de multiplicare a sumei de plată cu o valoare dată (*):
	Locatar operator*(float valoare) const
	{
		Locatar rezultat(*this);
		rezultat.suma_plata *= valoare;
		return rezultat;
	}

	//suport operatii binare

	 // Metodă pentru salvarea unui singur locatar într-un fișier binar
	void fisbinar_salvare(const string& Locatar) {
		ofstream fisierBinar(Locatar, ios::out | ios::binary);

		int lungimeNume = strlen(this->nume);
		fisierBinar.write((char*)&lungimeNume, sizeof(lungimeNume));
		fisierBinar.write(this->nume, lungimeNume);

		fisierBinar.write((char*)&this->nr_apartament, sizeof(nr_apartament));

		int lungimeLuna = this->luna.size();
		fisierBinar.write((char*)&lungimeLuna, sizeof(lungimeLuna));
		fisierBinar.write(this->luna.c_str(), lungimeLuna);

		fisierBinar.write((char*)&this->suma_plata, sizeof(suma_plata));

		fisierBinar.close();
	}

	// Metodă pentru încărcarea unui singur locatar dintr-un fișier binar
	void fisbinar_incarcare(const string& Locatar)
	{
		ifstream fisierBinar(Locatar, std::ios::in | std::ios::binary);
		if (fisierBinar.is_open()) {
			if (this->nume != nullptr) {
				delete[] this->nume;
			}

			int lungimeNume = 0;
			fisierBinar.read((char*)&lungimeNume, sizeof(lungimeNume));
			this->nume = new char[lungimeNume + 1];
			fisierBinar.read(this->nume, lungimeNume);
			this->nume[lungimeNume] = '\0';

			fisierBinar.read((char*)&this->nr_apartament, sizeof(nr_apartament));

			int lungimeLuna = 0;
			fisierBinar.read((char*)&lungimeLuna, sizeof(lungimeLuna));
			char* aux = new char[lungimeLuna + 1];
			fisierBinar.read(aux, lungimeLuna);
			aux[lungimeLuna] = '\0';
			this->luna = aux;
			delete[] aux;

			fisierBinar.read((char*)&this->suma_plata, sizeof(suma_plata));
		}
		else
		{
			cout << "Fisierul binar nu a fost gasit!" << endl;
		}
	}

	//scriere in fis text
	void fisiertext(const char* a)
	{
		ofstream fout(a);
		fout << nume<<endl;
		fout << nr_apartament << endl;
		fout << luna << endl;
		fout << suma_plata << endl;

		fout.close();
	}

	//citire in fis text

	void citiretext(const char* a)
	{
		ifstream fin(a);
		fin >> nume;
		fin >> nr_apartament;
		fin >> luna ;
		fin >> suma_plata ;

		fin.close();
	}
	
};


class Detalii_Locatar : public Locatar
{
private:
	int varsta;
	string numar_telefon;

public:

	Detalii_Locatar() : Locatar()
	{
		this->varsta = 0;
		this->numar_telefon = "-";
	}

	// constructor cu parametri

	Detalii_Locatar(int avarsta, string anumar_telefon, const char* nume, int apartament, string luna, float suma_plata) : Locatar(nume, apartament, luna, suma_plata)
	{
		this->varsta = avarsta;
		this->numar_telefon = anumar_telefon;
	}

	// constructor copiere

	Detalii_Locatar(const Detalii_Locatar& sursa) : Locatar(sursa)
	{
		this->varsta = sursa.varsta;
		this->numar_telefon = sursa.numar_telefon;
	}

	// operator egal

	Detalii_Locatar& operator=(const Detalii_Locatar& sursa)
	{
		Locatar::operator=(sursa);
		this->varsta = sursa.varsta;
		this->numar_telefon = sursa.numar_telefon;
		return *this;
	}

	// operator iesire

	friend ostream& operator<<(ostream& out, Detalii_Locatar& sursa)
	{
		out << (Locatar&)sursa << endl;
		out << ("Locatarul are varsta:") << sursa.varsta << endl;
		out << "Numarul de telefon este:" << sursa.numar_telefon << endl;
		return out;
	}

	// operator intrare

	friend istream& operator>>(istream& in, Detalii_Locatar& sursa)
	{
		in >> (Locatar&)sursa;
		cout << "Locatarul are varsta: ";
		in >> sursa.varsta;
		cout << "Locatarul are nr de telefon:" << endl;
		in >> sursa.numar_telefon;
		return in;
	}

	bool este_major() const
	{
		return varsta >= 18;
	}

	void schimbare_telefon(const string& noul_numar)
	{
		numar_telefon = noul_numar;
	}
};


class lista_locatar
{
private:
	Locatar* lista;
	int nr;

public:
	// Constructor implicit
	lista_locatar()
	{
		this->lista = NULL;
		this->nr = 0;
	}

	// Constructor cu parametri
	lista_locatar(Locatar* alista, int anr)
	{
		this->nr = anr;
		this->lista = new Locatar[anr];
		for (int i = 0; i < anr; i++)
		{
			this->lista[i] = alista[i];
		}
	}

	// Destructor
	~lista_locatar()
	{
		delete[] lista;
	}

	// Constructor de copiere
	lista_locatar(const lista_locatar& sursa)
	{
		this->nr = sursa.nr;
		this->lista = new Locatar[nr];
		for (int i = 0; i < nr; i++)
		{
			this->lista[i] = sursa.lista[i];
		}
	}

	// Operator atribuire
	lista_locatar& operator=(const lista_locatar& sursa)
	{
		if (this != &sursa)
		{
			delete[] lista;
			this->nr = sursa.nr;
			this->lista = new Locatar[nr];
			for (int i = 0; i < nr; i++)
			{
				this->lista[i] = sursa.lista[i];
			}
		}
		return *this;
	}

	// Metoda pentru calculul mediei valorilor din lista
	float calculMedie() const
	{
		if (nr == 0)
		{
			return 0;
		}
		float suma = 0;
		for (int i = 0; i < nr; ++i)
		{
			suma += lista[i];
		}

		return suma / nr;
	}

	// Metoda pentru găsirea celui mai mare element din lista
	float gasesteMaxim() const
	{
		if (nr == 0)
		{
			return Locatar();
		}
		Locatar maxim = lista[0];
		for (int i = 1; i < nr; ++i)
		{
			if (lista[i] > maxim)
			{
				maxim = lista[i];
			}
		}

		return maxim;
	}
	// Operator de iesire
	friend ostream& operator<<(ostream& out, const lista_locatar& sursa)
	{
		out << "Nr de locatari=" << sursa.nr << std::endl;
		for (int i = 0; i < sursa.nr; i++)
		{
			out << "Locatarul " << i << std::endl;
			out << sursa.lista[i] << std::endl;
		}
		return out;
	}

	// Operator de intrare
	friend istream& operator>>(std::istream& in, lista_locatar& sursa)
	{
		cout << "Nr de locatari=";
		in >> sursa.nr;
		sursa.lista = new Locatar[sursa.nr];
		for (int i = 0; i < sursa.nr; i++)
		{
			std::cout << "Locatarul ";
			in >> sursa.lista[i];
		}
		return in;
	}

	// Operator de adaugare de obiecte la lista
	lista_locatar operator+(const lista_locatar& l)
	{
		lista_locatar copie = *this;
		delete[] copie.lista;
		copie.nr = this->nr + l.nr;
		copie.lista = new Locatar[copie.nr];
		for (int i = 0; i < this->nr; i++)
		{
			copie.lista[i] = this->lista[i];
		}
		for (int i = this->nr; i < copie.nr; i++)
		{
			copie.lista[i] = l.lista[i - this->nr];
		}
		return copie;
	}

	// Operator de eliminare de obiecte din lista
	lista_locatar operator-(const lista_locatar& l)
	{
		lista_locatar copie = *this;
		delete[] copie.lista;
		copie.nr = this->nr - l.nr;
		copie.lista = new Locatar[copie.nr];
		for (int i = 0; i < copie.nr; i++)
		{
			copie.lista[i] = this->lista[i];
		}
		return copie;
	}

	// Operator de indexare
	Locatar& operator[](int index)
	{
		if (index >= 0 && index < this->nr)
		{
			return this->lista[index];
		}
	}

	void sortare()
	{
		for (int i = 0; i < nr - 1; ++i)
		{
			for (int j = 0; j < nr - i - 1; ++j)
			{
				if (lista[j] > lista[j + 1])
				{
					Locatar temp = lista[j];
					lista[j] = lista[j + 1];
					lista[j + 1] = temp;
				}
			}
		}
	}

};

class Datorii_locatar : public Locatar
{
private:
	bool datorii;
	int persoane_apartament;

public:

	//constructor fara parametri

	Datorii_locatar() : Locatar()
	{
		this->datorii = false;
		this->persoane_apartament = 0;
	}

	//constructor parametri

	Datorii_locatar(bool adatorii, int apersoane_apartament, const char* nume, int apartament, string luna, float suma_plata) : Locatar(nume, apartament, luna, suma_plata)
	{
		this->datorii = adatorii;
		this->persoane_apartament = apersoane_apartament;
	}

	//destructor
	~Datorii_locatar()
	{

	}

	//constructor copiere

	Datorii_locatar(const Datorii_locatar& sursa) : Locatar(sursa)
	{
		this->datorii = sursa.datorii;
		this->persoane_apartament = sursa.persoane_apartament;
	}

	//operator egal

	Datorii_locatar& operator=(const Datorii_locatar& sursa)
	{
		Locatar::operator=(sursa);
		this->datorii = sursa.datorii;
		this->persoane_apartament = sursa.persoane_apartament;
		return *this;
	}

	//operator iesire

	friend ostream& operator<<(ostream& out, Datorii_locatar& sursa)
	{
		out << (Locatar&)sursa << endl;
		out << (sursa.datorii ? "Locatarul are datorii!" : "Locatarul nu are datorii") << endl;
		out << "Numarul de persoane din apartament este:" << sursa.persoane_apartament << endl;
		return out;
	}

	//operator intrare

	friend istream& operator>>(istream& in, Datorii_locatar& sursa)
	{
		in >> (Locatar&)sursa;
		cout << "Nr de persoane din apartament este:";
		in >> sursa.persoane_apartament;
		cout << "Locatarul are datorii?" << endl;
		in >> sursa.datorii;
		return in;
	}


};
int main()
{
	Locatar L1;
	cout << "Locatar L1:" << endl << L1 << endl;

	Locatar L2;
	cout << "Locatar L1:" << endl << L2 << endl;

	//apel constructor cu parametri
	Locatar L3("George", 23, "Mai", 320);

	cout << "Locatar L3:" << endl << L3 << endl;

	//apel destructor
	Locatar* Li = new Locatar();
	delete Li;


	//apel constructor copiere

	Locatar L4(L3);

	cout << "Locatar L4:" << endl << L4 << endl;

	//apel operator egal
	L3 = L1;

	cout << "Locatar L1:" << endl << L1 << endl;

	//apel operator >> si <<

	Locatar L5;
	cout << "Introdu datele pentru L5:" << endl;
	cin >> L5;
	cout << "D5 date:" << endl << L5 << endl;


	// Exemplu de utilizare a operatorului <
	if (L1 < L2)
		cout << "Apartamentul a1 are o suma de plata mai mica decat a2." << endl;
	else
		cout << "Apartamentul a1 are o suma de plata mai mare sau egala cu a2." << endl;

	// Exemplu de utilizare a operatorului >
	if (L3 > L4)
		cout << "Apartamentul a3 are o suma de plata mai mare decat a4." << endl;
	else
		cout << "Apartamentul a3 are o suma de plata mai mica sau egala cu a4." << endl;

	// Utilizare conversie implicită către float
	float sumaPlataL3 = L3;
	cout << "Suma de plata pentru a3 este: " << sumaPlataL3 << endl;

	// Apel metoda afisareDetalii
	L3.afisareDetalii();

	// Apel metoda actualizareNume
	L3.actualizareNume("George Nou");

	// Apel metoda calculeazaTaxaSuplimentara
	float taxa = L3.calculeazaTaxaSuplimentara(5);
	cout << "Taxa suplimentara pentru locatarul L3 este: " << taxa << endl;


	//aduna valoarea folosind get si op+
	float sumaInitiala1 = L3.getSumaPlata();
	float valoareAdaugata = 50.5;

	Locatar L9 = L3 + valoareAdaugata;

	cout << "Suma de plata initiala: " << sumaInitiala1 << endl;
	cout << "Valoare adaugata: " << valoareAdaugata << endl;
	cout << "Suma de plata dupa adunare: " << L9.getSumaPlata() << endl;

	//apel get si operator-
	float sumaInitiala2 = L3.getSumaPlata();
	float valoareSczuta = 20.0;
	Locatar L10 = L3 - valoareSczuta;

	cout << "Suma de plata initiala: " << sumaInitiala2 << endl;
	cout << "Valoare scazuta: " << valoareSczuta << endl;
	cout << "Suma de plata dupa scadere: " << L10.getSumaPlata() << endl;

	//apel get si operator*
	float sumaInitiala3 = L3.getSumaPlata();
	float valoareInmultita = 1.5;

	Locatar L11 = L3 * valoareInmultita;
	cout << "Suma de plata initiala: " << sumaInitiala3 << endl;
	cout << "Valoare inmultita: " << valoareInmultita << endl;
	cout << "Suma de plata dupa inmultire: " << L11.getSumaPlata() << endl;


	// Exemplu de utilizare
	Locatar locatar;

	// Salvare în fișiere binare și text
	locatar.fisbinar_salvare("locatar_binar.dat");
	

	// Afisare din fișiere binare și text
	locatar.fisbinar_salvare("locatar_binar.dat");
	


	//apel constructor fara parametri lista_locatar

	lista_locatar ll1;
	cout << "Lista ll1:" << endl << ll1 << endl;

	lista_locatar ll2;
	cout << "Lista ll2:" << endl << ll2 << endl;

	//apel constructor parametri
	Locatar r[] = { Locatar("George", 23, "Mai", 320), Locatar("Mihai", 25, "Aprilie", 570) };
	lista_locatar ll3(r, 2);
	cout << "Lista ll3:" << endl << ll3 << endl;

	//apel destructor
	lista_locatar* lli = new lista_locatar();
	delete lli;
	cout << "Destructor apelat pentru lli" << endl;

	//apel constructor copiere

	lista_locatar ll4(ll3);
	cout << "Lista ll4:" << endl << ll4 << endl;
	cout << ll4 << endl;

	//apel operator egal
	ll3 = ll1;

	//apel op >> si >>

	lista_locatar ll5;
	cout << "Introdu datele pentru ll5:" << endl;
	cin >> ll5;
	cout << "ll5 date:" << endl << ll5 << endl;

	cout << "Lista initiala:\n" <<ll3;

	ll3.sortare();

	cout << "Lista sortata:\n" << ll3;

	// Testează metoda pentru calculul mediei
	cout << "\nMedia valorilor din lista: " << ll3.calculMedie() << endl;

	// Testează metoda pentru găsirea celui mai mare element
	cout << "Cel mai mare element din lista: " << ll3.gasesteMaxim() << endl;

	//Apel Constructor fara parameteri
	Datorii_locatar D1;
	cout << "Datorii_locatar D2:" << endl << D1 << endl;

	Datorii_locatar D2;
	cout << "Datorii_locatar D2:" << endl << D2 << endl;

	//Apel Constructor cu parameters
	Datorii_locatar D3(true, 3, "Ana", 15, "Iunie", 450);
	cout << "Datorii_locatar D2:" << endl << D2 << endl;

	//Apel Constructor copiere
	Datorii_locatar D4(D3);
	cout << D4 << endl;

	//Apel Operator atribuire
	D3 = D1;
	cout << D3 << endl;

	//Apel operator << si >> 
	Datorii_locatar D5;
	cout << "Introdu datele pentru D5:" << endl;
	cin >> D5;
	cout << "D5 date:" << endl << D5 << endl;

	//apelare si scriere fis text

	L3.fisiertext("LocatarL3.txt");
	Locatar L60;
	cout << "Citire text:" << endl;
	L60.citiretext("LocatarL3.txt");

	cout << L60;

	return 0;

}
