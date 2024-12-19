#include <stdio.h>
#include <stdlib.h>
#define RESET   "\033[0m"
#define RED     "\033[31m"
#define YELLOW  "\033[33m"
#define BLUE    "\033[34m"

typedef struct {
    int *positions;
    int count;
    int centre;
    int mask[8];
} PositionList;

typedef struct {
    PositionList isolated;
    PositionList *sequences;
    int sequence_count;
} Result;

Result findPositions(int *array, int size) {
    Result result;
    result.isolated.positions = (int*)malloc(size * sizeof(int));
    result.sequences = (PositionList*)malloc(size * sizeof(PositionList));
    for(int h=0; h<8; h++) result.sequences->mask[h] = 9;
    if (result.isolated.positions == NULL || result.sequences == NULL) {
        fprintf(stderr, "Errore di allocazione della memoria\n");
        exit(1);
    }
    result.isolated.count = 0;
    result.sequence_count = 0;
//  int array[8] = array2;
    int i = 0;
    while (i < size) {
        if (array[i] == 1) {
            // Controlla se è un 1 isolato
            if ((i == 0 && array[(i - 1 + size) % size] == 0) || (i == size - 1 && array[(i + 1) % size] == 0)) {
                result.isolated.positions[result.isolated.count++] = i;
                result.sequences->mask[i % size] = 1;
                i++;
            } else {
                // Inizia una nuova sequenza
                if ((i == 0 && array[(size-1) % size] == 1)){
                    do{
                    i++;
                    }while(array[i] == 1);
                }else{
                    result.sequences[result.sequence_count].positions = (int*)malloc(size * sizeof(int));
                    for(int h=0; h<8; h++) result.sequences[result.sequence_count].positions[h] = 9;
                    result.sequences[result.sequence_count].count = 0;
                    int start_seq = i % size;
                    int start = i;
                    int out = 0;
                    do {
                        result.sequences[result.sequence_count].positions[result.sequences[result.sequence_count].count++] = i % size;
                        result.sequences->mask[i % size] = 1;
                        i = (i + 1);
                        out = i % size;
                        result.sequences[result.sequence_count].centre = (start_seq + (result.sequences[result.sequence_count].count / 2)) % size; 
                    } while (array[out] == 1 && out != start);
                    if(result.sequences[result.sequence_count].count % 2 == 0) printf("\nErrore Configurazione: %d\n", result.sequences[result.sequence_count].count);
                    result.sequence_count++;
                }
            }
        } else {
            i++;
        }
    }

    return result;
}

void xnor(int *layer, int *cnfg, int position) {
    int count = 0;
    

    for (int i = 0; i < 8; i++) {
        for (int j = 0; j <= 7 ; j++) {
            if((i == cnfg[j]) && layer[i] == 1) count++;  
        }
    }
    //for(int i = 0 ; i <8; i++) printf("%d|", layer[i]);
    //printf("\n\n");
    //for(int i = 0 ; i <7+1; i++) printf("%d|", cnfg[i]);
   // printf("                COUNT %d, Centro in %d value %d    \n", count, position, (count % 2 == 0));
    layer[position] = (count % 2 == 0);
}

int main() {
    int array[4][8] = {
    {1, 1, 0, 1, 1, 1, 0, 1},
    {1, 1, 1, 0, 1, 0, 1, 1},
    {0, 1, 0, 1, 0, 1, 1, 1},
    {0, 1, 1, 1, 0, 1, 0, 1}
    };

    int input[] = {1, 1, 1, 0, 0, 0, 0, 0};
    int size = sizeof(array[4]) / sizeof(array[4][0]);
    Result result[4];
    for(int i = 0; i<4; i++){
        result[i] = findPositions(array[i], size);
    }

    for(int k = 0; k<4; k++){
        printf("\n\nArray originale: ");
        for (int j = 0; j < size; j++) {
            printf("%d ", array[k][j]);
        }
        printf("\n");

        printf("Posizioni degli 1 isolati: ");
        for (int i = 0; i < result[k].isolated.count; i++) {
            printf("%d ", result[k].isolated.positions[i]);
        }
        printf("\n");

        printf("Posizioni delle sequenze di 1:\n");
        for (int i = 0; i < result[k].sequence_count; i++) {
            printf("Sequenza %d: ", i + 1);
            for (int j = 0; j < result[k].sequences[i].count; j++) {
                printf("%d ", result[k].sequences[i].positions[j]);
            }
            printf("\nCentre %d", result[k].sequences[i].centre);
            printf("\n");
        }
        printf("Mask ");
        for (int j = 0; j < 8; j++) {
            printf("%d ", result[k].sequences->mask[j]);
        }
    }
    
    printf("\nProcessing:\n\n");
    bool t = false;
    printf(BLUE "INPUT     |");
    for (int j = 0; j < 8; j++) printf("%d|", input[j]); 

    printf(RESET);
    for(int i = 0; i < 4; i++){
        printf(YELLOW "\nREG%d      |", i);
        for (int j = 0; j < 8; j++) (result[i].sequences->mask[j] == 1) ? printf(YELLOW "@|") : printf(YELLOW "-|");
        printf("\n");
        for(int y=0; y<result[i].sequence_count; y++){
            //for(int l=0; l<8; l++)printf("_%d|",result[i].sequences[y].positions[l]);
            //printf("\n");
            xnor(input, result[i].sequences[y].positions, result[i].sequences[y].centre);
        }
        for(int y=0; y<result[i].isolated.count; y++){
            input[result[i].isolated.positions[y]] = !input[result[i].isolated.positions[y]];
        }
        (i == 3) ? printf(BLUE "OUTPUT    |", i, i+1) : printf(RED "OUT%d,IN%d  |", i, i+1);
        for (int j = 0; j < 8; j++) (i == 3) ? printf(BLUE "%d|", input[j]) :  printf(RED "%d|", input[j]);    
    }
  
    
    // Libera la memoria allocata
    for(int k = 0; k<4; k++){
        free(result[k].isolated.positions);
        for (int i = 0; i < result[k].sequence_count; i++) {
            free(result[k].sequences[i].positions);
        }
        free(result[k].sequences);
    }   

    printf("\n\n");
    return 0;
}
