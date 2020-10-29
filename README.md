# Algoritmo-Bellman-Ford
/*LIBERIAS*/
#include<stdio.h>
#include<stdlib.h>
/*DEFINIR VARIABLES INICIALES*/
#define MAX 100
#define infinity 9999
#define NIL -1
#define TRUE 1
#define FALSE 0
 
int n;    /*NUMERO DE VERTICES EN EL GRAFICO*/
int adj[MAX][MAX]; /*MATRIZ DE ADYACENCIA*/
 
int predecesor[MAX];
int LongCamino[MAX];
int esta_en_cola[MAX];
 
int frente,posterior;
int cola[MAX];
void inicializar_cola( );
void insertar_cola(int u);
int eliminar_cola();
int esta_vacia_cola();
void crear_topologia( );
void encontrarCamino(int s, int v);
int BellmanFord(int s);
 
int main()
{
        int flag,s,v;
 		/*s ES EL VERTICE DE ORIGEN*/
 		/*v ES EL VERTICE DE DESTINO*/
        crear_topologia();
 
        printf("\nIngrese el vertice de origen : ");
        scanf("%d",&s);
 
        flag = BellmanFord(s);
 
        if(flag == -1)
        {
                printf("\nError : Ciclo negativo en la grafica\n");
                exit(1);
        }
 
        while(1)
        {
                printf("\nIngrese el vertice de destino(-1 para salir): ");
                scanf("%d",&v);
                if(v == -1)
                        break;
                if(v < 0 || v >= n )
                        printf("\nEste vertice no existe\n");
                else if(v == s)
                        printf("\nLos vertices de origen y destino son los mismos\n");
                else if( LongCamino[v] == infinity )
            printf("\nNo hay ruta desde el vertice de origen hasta el de destino\n");
                else
                        encontrarCamino(s,v);
        }
        return 0;
}
 
 
void encontrarCamino(int s, int v )
{
        int i,u;
        /*ARREGLO QUE ALMACENA EL CAMINO MAS CORTO*/
        int camino[MAX];  
		/*VARIABLE QUE ALMACENA EL COSTO*/        
        int costo = 0;  
		/*NUMERO DE VERTICES EN EL CAMINO MAS CORTO*/    
        int contador = 0;          
 
        /*ALMACENA LA RUTA COMPLETA EN LA RUTA DE LA MATRIZ*/
        while( v != s )
        {
                contador++;
                camino[contador] = v;
                u = predecesor[v];
                costo += adj[u][v];
                v = u;
        }
        contador++;
        camino[contador]=s;
 
        printf("\nLa ruta mas corta es : ");
        for(i=contador; i>=1; i--)
                printf("%d  ",camino[i]);
        printf("\n El costo es : %d\n", costo);
}
 
int BellmanFord(int s)
{
        int k = 0,i,actual;
 
        for(i=0;i<n;i++)
        {
                predecesor[i] = NIL;
                LongCamino[i] = infinity;
                esta_en_cola[i] = FALSE;
        }
 
        inicializar_cola( );
         /*HACER LongCamino= 0 DEL VERTICE DE ORIGEN*/
        LongCamino[s] = 0;
        /*INSERTAR EL VERTICE DE ORIGEN EN LA COLA*/
        insertar_cola(s); 
    esta_en_cola[s] = TRUE;
        while( !esta_vacia_cola( ) )
        {
                actual = eliminar_cola( );
                esta_en_cola[actual] = FALSE;
                if(s == actual)
                        k++;
                if(k > n )
                		/*CICLO NEGATIVO ACCESIBLE DESDE EL VERTICE DE ORIGEN*/
                        return -1;
                for(i=0;i<n;i++)
                {
                        if ( adj[actual][i] != 0 )
                                if( LongCamino[i] > LongCamino[actual] + adj[actual][i] )
                                {
                                        LongCamino[i] = LongCamino[actual] + adj[actual][i];
                                        predecesor[i] = actual;
                                        if( !esta_en_cola[i] )
                                        {
                                                insertar_cola(i);
                                                esta_en_cola[i]=TRUE;
                                        }
                                }
                }
        }
        return 1;
}
 
void inicializar_cola( )
{
        int i;
        for(i=0;i<MAX;i++)
                cola[i] = 0;
        posterior = -1;frente = -1;
}
 
int esta_vacia_cola()
{
        if(frente == -1 || frente>posterior )
                return 1;
        else
                return 0;
 }
 
void insertar_cola(int added_item)
{
        if (posterior == MAX-1)
        {
                printf("\nQueue Overflow\n");
                exit(1);
        }
        else
        {
        		/*SI LA COLA ESTA INICIALMENTE VACIA */
                if (frente == -1)  
                        frente = 0;
                posterior = posterior+1;
                cola[posterior] = added_item ;
        }
}
 
int eliminar_cola()
{
        int d;
        if (frente == -1 || frente > posterior)
        {
                printf("\nQueue Underflow\n");
                exit(1);
        }
        else
        {
                d = cola[frente];
                frente=frente+1;
        }
        return d;
}
 
void crear_topologia()
{
        int i,max_borde,origen,destino, peso;
 
        printf("\nIngrese el numero de vertices : ");
        scanf("%d",&n);
        max_borde=n*(n-1);
 		int contador=1;
 		int nodo=0;
        for(i=1;i<=max_borde;i++)
        {
        		if(contador!= nodo){
                	origen=nodo;
                	destino=contador;
				}else{
                	origen=nodo;
                	destino=0;
				}
                if(contador==n-1){
                	contador=1;
                	nodo=nodo+1;
				}else{
					contador=contador+1;
				}
 
                printf("\nIngrese el peso del lado [ %d , %d ]:",origen, destino);
                scanf("%d",&peso);
                
 
                if( origen >= n || destino >= n || origen<0 || destino<0)
                {
                        printf("\nInvalid edge!\n");
                        i--;
                }
                else
                        adj[origen][destino] = peso;
        }
}
