# tetris

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_NOME 20
#define MAX_FILA 10
#define MAX_PILHA 3

// ----------------------------------------------
// STRUCT PRINCIPAL: PEÇA
// ----------------------------------------------
typedef struct {
    char nome[MAX_NOME];
    int id;
} Peca;

// ----------------------------------------------
// FILA DE FUTURAS PEÇAS
// ----------------------------------------------
typedef struct {
    Peca itens[MAX_FILA];
    int inicio;
    int fim;
    int qtd;
} Fila;

// ----------------------------------------------
// PILHA DE PEÇAS RESERVADAS
// ----------------------------------------------
typedef struct {
    Peca itens[MAX_PILHA];
    int topo;
} Pilha;

// ----------------------------------------------
// FUNÇÕES DA FILA
// ----------------------------------------------
void inicializarFila(Fila *f) {
    f->inicio = 0;
    f->fim = 0;
    f->qtd = 0;
}

int filaVazia(Fila *f) {
    return f->qtd == 0;
}

int filaCheia(Fila *f) {
    return f->qtd == MAX_FILA;
}

void enfileirar(Fila *f, Peca p) {
    if (filaCheia(f)) {
        printf("\nFila cheia! Não é possível adicionar mais peças.\n");
        return;
    }
    f->itens[f->fim] = p;
    f->fim = (f->fim + 1) % MAX_FILA;
    f->qtd++;
}

Peca desenfileirar(Fila *f) {
    Peca vazia = {"", -1};

    if (filaVazia(f)) {
        printf("\nFila vazia! Nenhuma peça para jogar.\n");
        return vazia;
    }

    Peca p = f->itens[f->inicio];
    f->inicio = (f->inicio + 1) % MAX_FILA;
    f->qtd--;
    return p;
}

void mostrarFila(Fila *f) {
    printf("\nPeças Futuras (Fila):\n");

    if (filaVazia(f)) {
        printf("  [vazia]\n");
        return;
    }

    int i = f->inicio;
    for (int c = 0; c < f->qtd; c++) {
        printf("  - %s (ID %d)\n", f->itens[i].nome, f->itens[i].id);
        i = (i + 1) % MAX_FILA;
    }
}

// ----------------------------------------------
// FUNÇÕES DA PILHA
// ----------------------------------------------
void inicializarPilha(Pilha *p) {
    p->topo = -1;
}

int pilhaVazia(Pilha *p) {
    return p->topo == -1;
}

int pilhaCheia(Pilha *p) {
    return p->topo == MAX_PILHA - 1;
}

void empilhar(Pilha *p, Peca x) {
    if (pilhaCheia(p)) {
        printf("\nPilha cheia! Não é possível reservar mais peças.\n");
        return;
    }
    p->itens[++p->topo] = x;
}

Peca desempilhar(Pilha *p) {
    Peca vazia = {"", -1};

    if (pilhaVazia(p)) {
        printf("\nNenhuma peça reservada!\n");
        return vazia;
    }

    return p->itens[p->topo--];
}

void mostrarPilha(Pilha *p) {
    printf("\nPeças Reservadas (Pilha):\n");

    if (pilhaVazia(p)) {
        printf("  [vazia]\n");
        return;
    }

    for (int i = p->topo; i >= 0; i--) {
        printf("  - %s (ID %d)\n", p->itens[i].nome, p->itens[i].id);
    }
}

// ----------------------------------------------
// MENU PRINCIPAL
// ----------------------------------------------
void menu() {
    printf("\n===== CONTROLE DE PEÇAS DO JOGO =====\n");
    printf("1. Jogar próxima peça\n");
    printf("2. Reservar a próxima peça\n");
    printf("3. Recuperar peça reservada\n");
    printf("4. Mostrar fila e pilha\n");
    printf("5. Sair\n");
    printf("Escolha: ");
}

// ----------------------------------------------
// PROGRAMA PRINCIPAL
// ----------------------------------------------
int main() {
    Fila fila;
    Pilha pilha;

    inicializarFila(&fila);
    inicializarPilha(&pilha);

    // Peças iniciais
    enfileirar(&fila, (Peca){"Quadrado", 1});
    enfileirar(&fila, (Peca){"Linha", 2});
    enfileirar(&fila, (Peca){"L", 3});
    enfileirar(&fila, (Peca){"Z", 4});

    int opcao;

    while (1) {
        menu();
        scanf("%d", &opcao);

        if (opcao == 1) {
            Peca p = desenfileirar(&fila);
            if (p.id != -1) {
                printf("\nVocê jogou a peça: %s\n", p.nome);
            }
        }

        else if (opcao == 2) {
            if (pilhaCheia(&pilha)) {
                printf("\nNão é possível reservar! Pilha cheia.\n");
                continue;
            }

            Peca p = desenfileirar(&fila);
            if (p.id != -1) {
                empilhar(&pilha, p);
                printf("\nPeça %s reservada.\n", p.nome);
            }
        }

        else if (opcao == 3) {
            Peca p = desempilhar(&pilha);
            if (p.id != -1) {
                printf("\nVocê recuperou a peça reservada: %s\n", p.nome);
            }
        }

        else if (opcao == 4) {
            mostrarFila(&fila);
            mostrarPilha(&pilha);
        }

        else if (opcao == 5) {
            printf("\nEncerrando...\n");
            break;
        }

        else {
            printf("\nOpção inválida!\n");
        }
    }

    return 0;
}
