#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_NOME 100

// Definindo a estrutura do registro de voto
typedef struct {
    int numero_cartao;
    char nome[MAX_NOME];
    int opcao_voto;
} Voto;

// Função para inserir votos no arquivo binário
void inserir_voto(char *nome_arquivo) {
    FILE *arquivo = fopen(nome_arquivo, "ab");
    if (arquivo == NULL) {
        perror("Erro ao abrir o arquivo para escrita");
        return;
    }

    Voto voto;
    printf("Digite o número do cartão: ");
    scanf("%d", &voto.numero_cartao);
    printf("Digite o nome do aluno: ");
    scanf(" %[^\n]", voto.nome); // Lê a string incluindo espaços
    printf("Digite a opção de voto (1, 2 ou 3): ");
    scanf("%d", &voto.opcao_voto);

    fwrite(&voto, sizeof(Voto), 1, arquivo);
    fclose(arquivo);
}

// Função para imprimir votos e gerar relatório
void imprimir_votos(char *arquivo_binario, char *arquivo_texto) {
    FILE *arquivo_bin = fopen(arquivo_binario, "rb");
    FILE *arquivo_txt = fopen(arquivo_texto, "w");
    if (arquivo_bin == NULL || arquivo_txt == NULL) {
        printf("Erro ao abrir os arquivos");
        return;
    }

    Voto voto;
    int contagem[4] = {0}; // Contagem para as chapas 1, 2 e 3

    while (fread(&voto, sizeof(Voto), 1, arquivo_bin) == 1) {
        fprintf(arquivo_txt, "Numero do Cartao: %d, Opcao de Voto: %d\n", voto.numero_cartao, voto.opcao_voto);
        if (voto.opcao_voto >= 1 && voto.opcao_voto <= 3) { // Verifica se o voto é válido
            contagem[voto.opcao_voto]++; // Contagem de votos
        }
    }

    fprintf(arquivo_txt, "Total de votos:\n");
    for (int i = 1; i <= 3; i++) {
        fprintf(arquivo_txt, "Chapa %d: %d\n", i, contagem[i]);
    }

    fclose(arquivo_bin);
    fclose(arquivo_txt);
}

// Função principal com menu
int main() {
    int opcao;
    char *arquivo_binario = "entrada.bin";
    char *arquivo_texto = "saida.txt";

    do {
        printf("Menu:\n");
        printf("1) Inserir Voto\n");
        printf("2) Imprimir Votos\n");
        printf("3) Sair\n");
        printf("Escolha uma opção: ");
        scanf("%d", &opcao);

        switch (opcao) {
            case 1:
                inserir_voto(arquivo_binario);
                break;
            case 2:
                imprimir_votos(arquivo_binario, arquivo_texto);
                break;
            case 3:
                printf("Saindo...\n");
                break;
            default:
                printf("Opção inválida. Tente novamente.\n");
                break;
        }
    } while (opcao != 3);

    return 0;
}




    
