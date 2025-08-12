# DAMARISGUEVARA_PROYEFINAL
PROYECTO FINAL
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>
#include <cctype>

using namespace std;

vector<string> palabrasProhibidas = {
     "BUEY", "CACA", "CACO", "CAGA", "CAGO", "CAGON","CAKA",
    "COGE", "COGI", "COJA", "COJER", "COJI", "COJO", "COJIDO","CULO", 
      "MARICA","KACA", "KAGA", "KAGO", "KOGE", "KOJO",
    "MAME", "MAMO", "MAMON", "MEAR", "MEAS", "MEON", "MION", "MOCO",
    
};

vector<string> rfcsGenerados;

string mayusculas(string s) {
    for (char &c : s) c = toupper(c);
    return s;
}

char primeraVocalInterna(const string &apellido) {
    for (size_t i = 1; i < apellido.size(); i++) {
        char c = toupper(apellido[i]);
        if (c == 'A' || c == 'E' || c == 'I' || c == 'O' || c == 'U') {
            return c;
        }
    }
    return 'X';
}

string limpiarCadena(string texto) {
    string resultado;
    string acentos = "ÁÉÍÓÚ";
    string sinAcentos = "AEIOU";

    for (char c : texto) {
        c = toupper(c);
        size_t pos = acentos.find(c);
        if (pos != string::npos) {
            c = sinAcentos[pos];
        }
        if (c != ' ') resultado += c;
    }
    return resultado;
}

string evitarPalabrasOfensivas(string rfc) {
    string inicio = rfc.substr(0, 4);
    for (string p : palabrasProhibidas) {
        if (inicio == p) {
            rfc[1] = 'X';
            break;
        }
    }
    return rfc;
}

string hacerRFCUnico(string rfc) {
    string original = rfc;
    int contador = 1;
    while (find(rfcsGenerados.begin(), rfcsGenerados.end(), rfc) != rfcsGenerados.end()) {
        rfc = original + to_string(contador);
        contador++;
    }
    rfcsGenerados.push_back(rfc);
    return rfc;
}

int main() {
    int cantidad;
    cout << "Ingrese la cantidad de personas: ";
    cin >> cantidad;
    cin.ignore();

    for (int i = 0; i < cantidad; i++) {
        string nombre, apellidoP, apellidoM;
        int dia, mes, año;

        cout << "\n=== Persona " << i + 1 << " ===\n";
        cout << "Apellido paterno: ";
        getline(cin, apellidoP);
        cout << "Apellido materno (o X si no tiene): ";
        getline(cin, apellidoM);
        cout << "Nombre: ";
        getline(cin, nombre);
        cout << "Dia de nacimiento (DD): ";
        cin >> dia;
        cout << "Mes de nacimiento (MM): ";
        cin >> mes;
        cout << "Año de nacimiento (AAAA): ";
        cin >> año;
        cin.ignore();

        apellidoP = limpiarCadena(apellidoP);
        apellidoM = limpiarCadena(apellidoM);
        nombre = limpiarCadena(nombre);

        // Construcción del RFC (sin homoclave)
        string rfc;
        rfc += apellidoP[0];
        rfc += primeraVocalInterna(apellidoP);
        rfc += (apellidoM != "X" && !apellidoM.empty()) ? apellidoM[0] : 'X';
        rfc += nombre[0];

        // Año, mes, día de nacimiento
        rfc += to_string(año).substr(2, 2);
        if (mes < 10) rfc += "0";
        rfc += to_string(mes);
        if (dia < 10) rfc += "0";
        rfc += to_string(dia);

        // Evitar palabras ofensivas
        rfc = evitarPalabrasOfensivas(rfc);

        // Hacer único el RFC
        rfc = hacerRFCUnico(rfc);

        cout << "RFC generado: " << mayusculas(rfc) << "\n";
    }

    return 0;
}
