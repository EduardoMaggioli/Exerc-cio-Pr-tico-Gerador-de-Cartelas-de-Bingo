Gerador-de-Cartelas-de-Bingo
Exercício prático em linguagem C - Gerador de Cartelas de Bingo.
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

#define LINHAS 5
#define COLUNAS 5
#define TAMANHO_FAIXA 15

/* Enum para representar as colunas B, I, N, G e O */
typedef enum {
    B = 1,
    I = 16,
    N = 31,
    G = 46,
    O = 61
} Coluna;

/* DESAFIO 3 - Struct para representar uma cartela */
typedef struct {
    int numeros[LINHAS][COLUNAS];
} Cartela;


/* ---------- PROTOTIPOS ---------- */

int gerarNumero(int minimo, int maximo);

int numeroExiste(
    int cartela[LINHAS][COLUNAS],
    int coluna,
    int numero
);

void ordenarColuna(
    int cartela[LINHAS][COLUNAS],
    int coluna
);

void gerarCartela(Cartela *cartela);

void imprimirCartela(Cartela cartela);

int cartelasIguais(
    Cartela cartela1,
    Cartela cartela2
);


/* ---------- PROGRAMA PRINCIPAL ---------- */

int main(void) {

    srand((unsigned int) time(NULL));

    int quantidade;

    printf("=================================\n");
    printf("   GERADOR DE CARTELAS DE BINGO\n");
    printf("=================================\n");

    /* DESAFIO 1 - Usuario escolhe a quantidade */
    printf("\nQuantas cartelas deseja gerar? ");

    if (scanf("%d", &quantidade) != 1 || quantidade <= 0) {

        printf("Quantidade invalida!\n");

        return 1;
    }


    /*
        DESAFIO 2
        Matriz tridimensional para armazenar
        todas as cartelas.
    */

    int cartelas3D[quantidade][LINHAS][COLUNAS];


    /*
        DESAFIO 3
        Vetor de structs para representar
        as cartelas.
    */

    Cartela cartelas[quantidade];


    /* Gera todas as cartelas */
    for (int i = 0; i < quantidade; i++) {

        int repetida;

        /*
            DESAFIO 5
            Garante que duas cartelas
            nao sejam identicas.
        */

        do {

            gerarCartela(&cartelas[i]);

            repetida = 0;


            for (int j = 0; j < i; j++) {

                if (cartelasIguais(cartelas[i], cartelas[j])) {

                    repetida = 1;

                    break;
                }
            }

        } while (repetida);


        /*
            Copia os numeros da struct para
            a matriz tridimensional.

            Assim o Desafio 2 tambem fica
            implementado.
        */

        for (int linha = 0; linha < LINHAS; linha++) {

            for (int coluna = 0; coluna < COLUNAS; coluna++) {

                cartelas3D[i][linha][coluna] =
                    cartelas[i].numeros[linha][coluna];
            }
        }
    }


    /* Exibe todas as cartelas */
    for (int i = 0; i < quantidade; i++) {

        printf("\n\n===== CARTELA %d =====\n", i + 1);

        imprimirCartela(cartelas[i]);
    }


    return 0;
}


/* ---------- FUNCOES ---------- */


/*
    Gera um numero aleatorio
    entre minimo e maximo.
*/

int gerarNumero(int minimo, int maximo) {

    return (rand() % (maximo - minimo + 1)) + minimo;
}


/*
    Verifica se um numero ja existe
    dentro da coluna.
*/

int numeroExiste(
    int cartela[LINHAS][COLUNAS],
    int coluna,
    int numero
) {

    for (int linha = 0; linha < LINHAS; linha++) {

        if (cartela[linha][coluna] == numero) {

            return 1;
        }
    }

    return 0;
}


/*
    DESAFIO 4
    Ordena os numeros de uma coluna
    em ordem crescente.
*/

void ordenarColuna(
    int cartela[LINHAS][COLUNAS],
    int coluna
) {

    /*
        Na coluna N existe o espaco livre
        na posicao central.

        Por isso os numeros sao separados
        antes da ordenacao.
    */

    int numeros[LINHAS];

    int quantidade = 0;


    for (int linha = 0; linha < LINHAS; linha++) {

        /*
            Ignora somente o centro
            da coluna N.
        */

        if (coluna == 2 && linha == 2) {

            continue;
        }

        numeros[quantidade] =
            cartela[linha][coluna];

        quantidade++;
    }


    /* Bubble Sort */

    for (int i = 0; i < quantidade - 1; i++) {

        for (int j = 0; j < quantidade - 1 - i; j++) {

            if (numeros[j] > numeros[j + 1]) {

                int auxiliar = numeros[j];

                numeros[j] = numeros[j + 1];

                numeros[j + 1] = auxiliar;
            }
        }
    }


    /*
        Coloca os numeros ordenados
        novamente na cartela.
    */

    int indice = 0;


    for (int linha = 0; linha < LINHAS; linha++) {

        if (coluna == 2 && linha == 2) {

            cartela[linha][coluna] = 0;

        } else {

            cartela[linha][coluna] =
                numeros[indice];

            indice++;
        }
    }
}


/*
    Gera uma cartela completa.
*/

void gerarCartela(Cartela *cartela) {

    /*
        Limpa toda a matriz antes
        de gerar os numeros.
    */

    for (int linha = 0; linha < LINHAS; linha++) {

        for (int coluna = 0; coluna < COLUNAS; coluna++) {

            cartela->numeros[linha][coluna] = 0;
        }
    }


    /*
        Valores iniciais de cada coluna:

        B = 1
        I = 16
        N = 31
        G = 46
        O = 61
    */

    int inicio[COLUNAS] = {
        B,
        I,
        N,
        G,
        O
    };


    for (int coluna = 0; coluna < COLUNAS; coluna++) {

        int minimo = inicio[coluna];

        int maximo =
            minimo + TAMANHO_FAIXA - 1;


        for (int linha = 0; linha < LINHAS; linha++) {

            /*
                Posicao central:
                linha 3 e coluna 3.

                Em C os indices comecam em 0,
                portanto corresponde a [2][2].
            */

            if (linha == 2 && coluna == 2) {

                cartela->numeros[linha][coluna] = 0;

                continue;
            }


            int numero;


            /*
                Gera numeros ate encontrar
                um que ainda nao exista
                naquela coluna.
            */

            do {

                numero =
                    gerarNumero(minimo, maximo);

            } while (

                numeroExiste(
                    cartela->numeros,
                    coluna,
                    numero
                )

            );


            cartela->numeros[linha][coluna] =
                numero;
        }


        /*
            DESAFIO 4
            Ordena a coluna.
        */

        ordenarColuna(
            cartela->numeros,
            coluna
        );
    }
}


/*
    DESAFIO 5
    Compara duas cartelas para verificar
    se todos os numeros sao iguais.
*/

int cartelasIguais(
    Cartela cartela1,
    Cartela cartela2
) {

    for (int linha = 0; linha < LINHAS; linha++) {

        for (int coluna = 0; coluna < COLUNAS; coluna++) {

            if (
                cartela1.numeros[linha][coluna]
                !=
                cartela2.numeros[linha][coluna]
            ) {

                return 0;
            }
        }
    }


    return 1;
}


/*
    Exibe a cartela de forma organizada.
*/

void imprimirCartela(Cartela cartela) {

    printf("\n");

    printf(" B    I    N    G    O\n");

    printf("-----------------------\n");


    for (int linha = 0; linha < LINHAS; linha++) {

        for (int coluna = 0; coluna < COLUNAS; coluna++) {

            /*
                Espaco livre central.
            */

            if (linha == 2 && coluna == 2) {

                printf(" X   ");

            } else {

                printf(
                    "%2d   ",
                    cartela.numeros[linha][coluna]
                );
            }
        }


        printf("\n");
    }
}
