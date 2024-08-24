%option noyywrap
%x c_p
%{
        int posicaox = 1, posicaoy = 1, direcaoR = 1, PosicaoPinca = 1, EstadoP = 2, Varaux = 0;
        void Mostrardirecao();
        void Serro();
        void Andar();
        void MostrarPosicao();
        void ManipularString();
        void Rodar();
        void ControloPinca();
        void MostrarPosicaoPinca();
        void MostrarEstadoPinca();
%}
%%

VIRAR-DIREITA {

                direcaoR++;
        if(direcaoR == 5){ direcaoR = 1;}
}
VIRAR-ESQUERDA {

                direcaoR--;
                if(direcaoR == 0){ direcaoR = 4;}
}
ANDAR\((100|[1-9][0-9]|[1-9])\) {
                ManipularString();
                Andar();
}
"PINCA("    BEGIN(c_p);
<c_p>"ABRIR" {  if(EstadoP == 1)  printf("Erro, a pinca ja se encontra aberta"); else EstadoP = 1; }
<c_p>"FECHAR"  { if(EstadoP == 2) printf("Erro, a pinca ja se encontra fechada"); else EstadoP = 2;  }
<c_p>")"    BEGIN(INITIAL);

RODAR\((-)?(45|90|135|180|270|315|360)\) {

                ManipularString();
                Rodar();
                ControloPinca();
}
. {}
\n {}
EOF {}
%%

int main()
{
        yylex();
        Mostrardirecao();
        MostrarPosicao();
        MostrarPosicaoPinca();
        MostrarEstadoPinca();

}

void Mostrardirecao()
{
        if(direcaoR == 1){
                printf("O robot está virado para norte \n");
        }
        if(direcaoR == 2){
                printf("O robot está virado para este \n");
        }
        if(direcaoR == 3){
                printf("O robot está virado para sul \n");
        }
        if(direcaoR == 4){
                printf("O robot está virado para oeste \n");
        }
}
void Serro()
{
        printf("Erro no comando %s, nao foi efetuado nenhuma operacao \n",yytext);
}
void ManipularString()
{
char aux[4]="";
int i = 0 ;
        Varaux = 0;

        if(yytext[6] == '-'){
        i = 1;
        aux[0] = yytext[6];
        }

        if(yytext[7 + i] == ')'){
        aux[0 + i] = yytext[6 + i];
        }

        if(yytext[8 + i] == ')'){
        aux[0 + i] = yytext[6 + i];
        aux[1 + i] = yytext[7 + i];
        }

        if(yytext[9 + i] == ')'){
        aux[0 + i] = yytext[6 + i];
        aux[1 + i] = yytext[7 + i];
        aux[2 + i] = yytext[8 + i];
        }
        Varaux = atoi(aux);
}
void Andar()
{
        if(direcaoR == 1){
                posicaoy = posicaoy + Varaux;
                if ( posicaoy > 100){
                Serro();
                posicaoy = posicaoy - Varaux;
                }
        }
        if(direcaoR == 2){
                posicaox = posicaox + Varaux;
                if ( posicaox > 100){
                Serro();
                posicaox = posicaox - Varaux;
                }
        }if(direcaoR == 3){
                posicaoy = posicaoy - Varaux;
                if ( posicaoy < 1){
                Serro();
                posicaoy = posicaoy + Varaux;
                }
        }if(direcaoR == 4){
                posicaox = posicaox - Varaux;
                if ( posicaox < 1){
                Serro();
                posicaox = posicaox + Varaux;
                }
        }
}
void MostrarPosicao()
{
        printf("A posicao do robot é %d x %d \n", posicaox, posicaoy);
}
void Rodar()
{
        if(Varaux == 0 || Varaux == 360) {
                PosicaoPinca = PosicaoPinca;
        }
        else if(Varaux == 45 || Varaux == -315) {
                PosicaoPinca = PosicaoPinca + 1;
        }
        else if(Varaux == 90 || Varaux == -270) {
                PosicaoPinca = PosicaoPinca + 2;
        }
        else if(Varaux == 135 || Varaux == -225) {
                PosicaoPinca = PosicaoPinca + 3;
        }
        else if(Varaux == 180 || Varaux == -180) {
                PosicaoPinca = PosicaoPinca + 4;
        }
        else if(Varaux == 225 || Varaux == -135) {
                PosicaoPinca = PosicaoPinca - 3;
        }
        else if(Varaux == 270 || Varaux == -90) {
                PosicaoPinca = PosicaoPinca - 2;
        }
        else if(Varaux == 315 || Varaux == -45) {
                PosicaoPinca = PosicaoPinca - 1;
	}
        else{
                printf("O comando introduzido nao altera a posicao da pinca. \n");
                Serro();
        }
}
void ControloPinca()
{
        if(PosicaoPinca == 9) {PosicaoPinca = 1;}
        if(PosicaoPinca == 10) {PosicaoPinca = 2;}
        if(PosicaoPinca == 11) {PosicaoPinca = 3;}
        if(PosicaoPinca == 12) {PosicaoPinca = 4;}
        if(PosicaoPinca == 0) {PosicaoPinca = 8;}
        if(PosicaoPinca == -1) {PosicaoPinca = 7;}
        if(PosicaoPinca == -2) {PosicaoPinca = 6;}
}
void MostrarPosicaoPinca()
{
        if(PosicaoPinca ==1) {printf("A pinca esta virada para Norte \n ");}
        if(PosicaoPinca ==2) {printf("A pinca esta virada para Nordeste \n ");}
        if(PosicaoPinca ==3) {printf("A pinca esta virada para Este \n ");}
        if(PosicaoPinca ==4) {printf("A pinca esta virada para Sudeste \n ");}
        if(PosicaoPinca ==5) {printf("A pinca esta virada para Sul \n ");}
        if(PosicaoPinca ==6) {printf("A pinca esta virada para Sudoeste \n ");}
        if(PosicaoPinca ==7) {printf("A pinca esta virada para Oeste \n ");}
        if(PosicaoPinca ==8) {printf("A pinca esta virada para Nordeste \n ");}
}
void MostrarEstadoPinca()
{
        if(EstadoP == 2) {
                printf("A pinca esta fechada \n");
        }else{
                printf("A pinca esta aberta \n");
        }
}