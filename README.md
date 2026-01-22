# CRUD-simples-em-C
projeto CRUD simples usando logica de subrotinas e loop do while.

Codigo:

#include <stdio.h>
#include <stdlib.h>

#define FILE_NAME "alunos.dat"

typedef struct {
    int id;
    char nome[50];
    float nota;
} Aluno;

/* Funções */
void adicionarAluno();
void listarAlunos();
void atualizarAluno();
void removerAluno();
void menu();

int main() {
    menu();
    return 0;
}

void menu() {
    int opcao;

    do {
        printf("\n=== SISTEMA DE ALUNOS ===\n");
        printf("1 - Adicionar aluno\n");
        printf("2 - Listar alunos\n");
        printf("3 - Atualizar aluno\n");
        printf("4 - Remover aluno\n");
        printf("0 - Sair\n");
        printf("Escolha: ");
        scanf("%d", &opcao);

        switch (opcao) {
            case 1: adicionarAluno(); break;
            case 2: listarAlunos(); break;
            case 3: atualizarAluno(); break;
            case 4: removerAluno(); break;
            case 0: printf("Saindo...\n"); break;
            default: printf("Opcao invalida!\n");
        }
    } while (opcao != 0);
}

void adicionarAluno() {
    FILE *file = fopen(FILE_NAME, "ab");
    Aluno a;

    if (!file) {
        printf("Erro ao abrir arquivo.\n");
        return;
    }

    printf("ID: ");
    scanf("%d", &a.id);
    printf("Nome: ");
    scanf(" %[^\n]", a.nome);
    printf("Nota: ");
    scanf("%f", &a.nota);

    fwrite(&a, sizeof(Aluno), 1, file);
    fclose(file);

    printf("Aluno adicionado com sucesso!\n");
}

void listarAlunos() {
    FILE *file = fopen(FILE_NAME, "rb");
    Aluno a;

    if (!file) {
        printf("Nenhum registro encontrado.\n");
        return;
    }

    printf("\n--- LISTA DE ALUNOS ---\n");
    while (fread(&a, sizeof(Aluno), 1, file)) {
        printf("ID: %d | Nome: %s | Nota: %.2f\n", a.id, a.nome, a.nota);
    }

    fclose(file);
}

void atualizarAluno() {
    FILE *file = fopen(FILE_NAME, "rb+");
    Aluno a;
    int id, encontrado = 0;

    if (!file) {
        printf("Arquivo nao encontrado.\n");
        return;
    }

    printf("Informe o ID do aluno: ");
    scanf("%d", &id);

    while (fread(&a, sizeof(Aluno), 1, file)) {
        if (a.id == id) {
            printf("Novo nome: ");
            scanf(" %[^\n]", a.nome);
            printf("Nova nota: ");
            scanf("%f", &a.nota);

            fseek(file, -sizeof(Aluno), SEEK_CUR);
            fwrite(&a, sizeof(Aluno), 1, file);
            encontrado = 1;
            break;
        }
    }

    fclose(file);

    if (encontrado)
        printf("Aluno atualizado.\n");
    else
        printf("Aluno nao encontrado.\n");
}

void removerAluno() {
    FILE *file = fopen(FILE_NAME, "rb");
    FILE *temp = fopen("temp.dat", "wb");
    Aluno a;
    int id, encontrado = 0;

    if (!file || !temp) {
        printf("Erro ao abrir arquivos.\n");
        return;
    }

    printf("Informe o ID do aluno a remover: ");
    scanf("%d", &id);

    while (fread(&a, sizeof(Aluno), 1, file)) {
        if (a.id == id) {
            encontrado = 1;
        } else {
            fwrite(&a, sizeof(Aluno), 1, temp);
        }
    }

    fclose(file);
    fclose(temp);

    remove(FILE_NAME);
    rename("temp.dat", FILE_NAME);

    if (encontrado)
        printf("Aluno removido.\n");
    else
        printf("Aluno nao encontrado.\n");
}
