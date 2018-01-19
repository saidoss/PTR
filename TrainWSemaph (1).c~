#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <pthread.h>
#include <semaphore.h>

#define LOOPS 5    //set how many times the threads will run


struct train{
    int trainNumber;
    char *route;
    int stationAct;
    int stationNext;
    int index;      //The index of the stationNext in the string route of this struct
    int indexMaxSize;
    int time;
};

int matLines[5][5] = {{0}}; //rows=starts  columns=ends  this matrix contains the lines activity 0 = free 1 = busy

 sem_t *semaphTrainTravel;  //semaphore for use in trainTravel procedure
 sem_t *semaphUpStation;    //semaphore for use in updateStation procedure

 pthread_mutex_t mLine; /* mutex declaration for lines*/
 pthread_mutex_t mStation; /* mutex declaration for stations*/


/*global struct for each train*/
//A=0   B=1   C=2   D=3   E=4

 struct train STrain1 = {1,"01210", 0,1,1,4,1}; //ABCBA
 struct train STrain2 = {2,"013210",0,1,1,5,1}; //ABDCBA
 struct train STrain3 = {3,"013241",0,1,1,5,1}; //ABDCEA


 int contTrainTry1=0,contTrainTry2=0,contTrainTry3=0;  //trains try to travel counters

 int contTrain1=0,contTrain2=0,contTrain3=0;   //trains travel counters

 int contStat0=0,contStat1=0,contStat2=0,contStat3=0,contStat4=0;  //stations used counters

 //time acummulators to calculate average
 float T1time=0;
 float T2time=0;
 float T3time=0;



void updateStation(struct train *STrainTravel){    /*update the train stationAct and stationNext*/


    char *auxChar;   //auxiliary variable to do data type conversions

    if (STrainTravel->index < STrainTravel->indexMaxSize){     //if next station is not the last of the route for this train

            //pthread_mutex_lock(&mStation);
            sem_wait(&semaphUpStation); // blocks if semaphore is 0. If semaphore nonzero,
                                          // it decrements semaphore and proceeds

            STrainTravel->stationAct = STrainTravel->stationNext; //once arrived we assign the reached station to the parameter

            STrainTravel->index= STrainTravel->index+1; // increment the value for the next station

            auxChar = (STrainTravel->route[STrainTravel->index]);

            STrainTravel->stationNext = atoi(&auxChar); //now STrainTravel.stationNext has the value of index incremented in 1


         }

    else if (STrainTravel->index == STrainTravel->indexMaxSize){

            STrainTravel->stationAct = STrainTravel->indexMaxSize;
            auxChar = STrainTravel->route[0];
            STrainTravel->stationNext = atoi(&auxChar);
            STrainTravel->index = 0;
       }
        //pthread_mutex_unlock(&mStation);
        sem_post(&semaphUpStation); // sem_post increments semaphore, here release the sem_wait
};



  void trainTravel(struct train *STrainTravel){

    /* Generate a random number: */
    STrainTravel->time = rand() % 3 + 1; //replace the default value defined when the train struct data was filled.

    if(STrainTravel->trainNumber == 1)
        contTrainTry1++; //counts the number of times that TrainN try to travel
    else if (STrainTravel->trainNumber == 2)
        contTrainTry2++;
    else
        contTrainTry3++;


    if(matLines[STrainTravel->stationAct][STrainTravel->stationNext] == 0){  //if the line is free


             sem_wait(&semaphTrainTravel); // blocks if semaphore is 0. If semaphore nonzero,
                                          // it decrements semaphore and proceeds

             if (STrainTravel->trainNumber == 1){
                contTrain1++;
                T1time += STrainTravel->time;
                } //counts the number of times that TrainN traveled
             else if (STrainTravel->trainNumber == 2){
                contTrain2++;
                T2time += STrainTravel->time;
             }
             else{
                contTrain3++;
                T3time += STrainTravel->time;
             }





            printf("The train number: %d is traveling from station: %d to station: %d\n",STrainTravel->trainNumber,STrainTravel->stationAct,STrainTravel->stationNext);
            matLines[STrainTravel->stationAct][STrainTravel->stationNext] = 1; //set line to busy
            sleep(STrainTravel->time);  //wait for the train to arrive to the destiny station
            matLines[STrainTravel->stationAct][STrainTravel->stationNext] = 0;  //set line to free
            printf("The train number: %d has arrived to the station: %d\n",STrainTravel->trainNumber,STrainTravel->stationNext);



            if (STrainTravel->stationNext == 0 || STrainTravel->stationNext == 5)
                contStat0++;
            else if(STrainTravel->stationNext == 1)
                contStat1++;
            else if(STrainTravel->stationNext == 2)
                contStat2++;
            else if(STrainTravel->stationNext == 3)
                contStat3++;
            else contStat2++;


            sem_post(&semaphTrainTravel); // sem_post increments semaphore, here release the sem_wait
        }

        //calculate the trains next station
     updateStation(STrainTravel);


 };


int main() {

    int res; //var for the result number when init semaphTrainTravel
    int x; // var used in for loop
    srand ( time(NULL) ); /* initialize random seed with the computer time */

    pthread_t ThdTrain1,ThdTrain2,ThdTrain3;  /*thread declaration for each train*/

    /*pthread_mutex_init(&mLine, NULL);
    pthread_mutex_init(&mStation, NULL);*/


    /* initialize semaphores */
    res = sem_init(&semaphTrainTravel,  /* pointer to semaphore */
                                           0 ,  /* 0 if shared between threads, 1 if shared between processes */
                                           1);  /* initial value for semaphore (0 is locked / 1 is unlock) */
    if(res < 0)
    {
        perror("Semaphore semaphTrainTravel initialization failed");
        exit(0);
    }
    if (sem_init(&semaphUpStation, 0, 1)) /* initially unlocked */
    {
        perror("Semaphore semaphUpStation initialization failed");
        exit(0);
    }




   // while(1){   //infinite loop
   for(x = 0;x < LOOPS;x++){

        pthread_create(&ThdTrain1,NULL, (void*)trainTravel,(void*)&STrain1);

        pthread_create(&ThdTrain2,NULL, (void*)trainTravel,(void*)&STrain2);

        pthread_create(&ThdTrain3,NULL, (void*)trainTravel,(void*)&STrain3);

    }

 for(x = 0;x < LOOPS;x++){
 // Waiting for the threads to finish

    pthread_join(ThdTrain1, NULL);
    pthread_join(ThdTrain2, NULL);
    pthread_join(ThdTrain3, NULL);
 }

    /* finish the mutex*/
    //pthread_mutex_destroy(&mLine);
    //pthread_mutex_destroy(&mStation);


    sem_destroy(&semaphTrainTravel);
    sem_destroy(&semaphUpStation);

    printf("The threads has been finished.\n\n\n");

    printf("The train number: 1, try to travel: %d times\n",contTrainTry1);
    printf("The train number: 2, try to travel: %d times\n",contTrainTry2);
    printf("The train number: 3, try to travel: %d times\n\n\n",contTrainTry3);


    printf("The train number: 1, traveled: %d times and the average travel time was: %f\n",contTrain1,(T1time/contTrain1));
    printf("The train number: 2, traveled: %d times and the average travel time was: %f\n",contTrain2,(T2time/contTrain2));
    printf("The train number: 3, traveled: %d times and the average travel time was: %f\n\n\n",contTrain3,(T3time/contTrain3));


    printf("The station number: 0, was visited: %d times\n",contStat0);
    printf("The station number: 1, was visited: %d times\n",contStat1);
    printf("The station number: 2, was visited: %d times\n",contStat2);
    printf("The station number: 3, was visited: %d times\n",contStat3);
    printf("The station number: 4, was visited: %d times\n",contStat4);


    return 0;
}

