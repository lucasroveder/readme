//
#include <iostream>
#include <vector>
#include <random>
#include <functional>
#include <chrono>
#include <algorithm>
#include <iomanip>
#include <cassert>
#include <iterator>

using namespace std;

template<typename T>
void Selecao(vector<T>& A) {
    int countA[A.size()];
    for (int i = 0; i < A.size(); ++i) {
        countA[i] = 0;
    }
    for (int i = 0; i < A.size(); i++) {
        countA[A[i]]++;
    }
    for (int i = 1; i < A.size(); i++) {
        countA[i] += countA[i - 1];
    }
    vector<T> A_copy = A;
    for (int i = A.size() - 1; i >= 0; i--) {
        A[countA[A_copy[i]] - 1] = A_copy[i];
        countA[A_copy[i]]--;
    }
}

int main(void)
{
    auto n = 100000;
    vector<int> A(n);
    vector<int> Acopia(n);
    float ganho = 0.0;

     // a semente e o tempo desde 1/1/1970
    auto seed = chrono::high_resolution_clock::now().time_since_epoch().count();
    default_random_engine r1(seed);
    uniform_int_distribution<int> uniform_dist(0, 10000);

    // gera entrada aleatoria
    auto rand = bind(uniform_dist, r1);
    generate( A.begin(), A.end(), rand );
    // faz copia de verificacao
    copy( A.begin(), A.end(), Acopia.begin() );

    // imprime entrada
    //copy( A.begin(), A.end(), ostream_iterator<int>(cout, " ") );
    //cout << endl;

    {
        // teste do algoritmo 
        auto inicio = chrono::system_clock::now();
        Selecao( A );
        auto fim = chrono::system_clock::now();
        auto tempo = chrono::duration_cast<chrono::microseconds>(fim-inicio).count();
        //cout << setprecision(4) << tempo << " (us)" << endl;
        ganho = tempo;
    }
 
    {
        // ordena original e tempo
        auto inicio = chrono::system_clock::now();
        sort( Acopia.begin(), Acopia.end() );
        auto fim = chrono::system_clock::now();
        auto tempo = chrono::duration_cast<chrono::microseconds>(fim-inicio).count();
        //cout << setprecision(4) << tempo << " (us)" << endl;
        ganho = tempo/ganho;
    }

    // testamos a validade do resultado
    if (equal( A.begin(), A.end(), Acopia.begin() ) )
        cout << setprecision(4) << ganho << endl;
    else
        cout << "0" << endl;

    return 0;
}
