# A basic preflop for deep stack NL

## Factors affecting preflop decisions

The first things we need are the basic tools, so we need to think about
the factors which affect the decision preflop.

- type of hand

- position

- current hand scenario (first in, limped pot, raised pot, 3bet pot
  etc.)

Obviously there is more, like which types of opponents are still playing
in the hand, what is the table image, but we will neglect opponent
moddeling for this article.

## Basic Functions

### Type of Hand

This is pretty easy, we only need rankhiplayer, rankloplayer, issuited
to determine your hand. You can always simply get these values with the
gws() function, but I will extract them from the holdem_state struct,
it’s more examples for you and it’s better to avoid gws() calls as often
as possible.

These are the vars we will define to determine the type of hand:

    bool issuited; 
    int card[7][2]; 
    int rankhiplayer; 
    int rankloplayer; 
    int gap;            // 0 = pair, 1 = connected, 2 = one-gapper etc...

First we need a function to set our card values:

    void set_card_values(){     /*      sets the card values    */
      //pocket cards    card[0][0] = RANK(m_holdem_state[m_ndx].m_player[userchair].m_cards[0]);
      card[0][1] = SUIT(m_holdem_state[m_ndx].m_player[userchair].m_cards[0]);
      card[1][0] = RANK(m_holdem_state[m_ndx].m_player[userchair].m_cards[1]);
      card[1][1] = SUIT(m_holdem_state[m_ndx].m_player[userchair].m_cards[1]);
      //board cards     
      for(int u=0;u<5;u++){         
        card[u+2][0] = RANK(m_holdem_state[(m_ndx)&0xff].m_cards[u]);
        card[u+2][1] = SUIT(m_holdem_state[(m_ndx)&0xff].m_cards[u]);
      } 
    }

Note that we used the card macros RANK & SUIT, which were provided with
the basic dll. These macros convert the unsigned char values to integer
values:

rank 2 ; ... ; 14=A suit 1=C ; 2=D ; 3=H ; 4=S

Now we can define the functions to set our variables:

    int set_rankhiplayer(){     
      /*        returns hi player rank  */  
      if(card[0][0] > card[1][0]){
        return card[0][0];  
      } else {
        return card[1][0];
      }
    } 

    int set_rankloplayer(){
      /*        returns lo player rank  */
      if(card[0][0] > card[1][0]){
        return card[1][0];
      } else {      
        return card[0][0];
      } 
    } 

    bool set_suited() {     
      /*        1 - suited ; 0 -unsuited    */  
      if(card[0][1] == card[1][1]) {
        return 1;   
      } else {
        return 0;
      } 
    } 

    int set_gap() {     
      /*        0 - pair ; 1 - connected ; 2 - one-gapper ...   */  
      return rankhiplayer-rankloplayer; 
    }

### Position

I will use the following convention here, since i’ve grown quite
familiar to it and it makes a lot of sense.

    7 UTG  6 UTG+1  5 UTG+2  4 MP1  3 MP2  2 MP3  1 CO  0 BU -1 SB -2 BB

The first problem you can encounter at some pokersites is that some
actually allow hands without a small blind. We need to take care of
that:

    bool set_SB(){  /*      checks if sblind was posted at the table    */  for(int i=0;i<10;i++){      if(m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet < bblind-0.01){            return 1;       }   }
        return 0;
    }

The function above determins if there is a small blind present at the
table. The function below determins our position:

    int pf_pos(){   /*      returns the current position    */  int dealposition = (int)gws("dealposition");    int nopponentsdealt = (int)gws("nopponentsdealt");
        if(SB){         if(nopponentsdealt == 1){           int pos[2] = {{-2},{-1}};           return pos[dealposition-1];         }       if(nopponentsdealt == 2){           int pos[3] = {{-1},{-2},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 3){           int pos[4] = {{-1},{-2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 4){           int pos[5] = {{-1},{-2},{2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 5){           int pos[6] = {{-1},{-2},{3},{2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 6){           int pos[7] = {{-1},{-2},{4},{3},{2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 7){           int pos[8] = {{-1},{-2},{5},{4},{3},{2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 8){           int pos[9] = {{-1},{-2},{6},{5},{4},{3},{2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 9){           int pos[10] = {{-1},{-2},{7},{6},{5},{4},{3},{2},{1},{0}};          return pos[dealposition-1];         }   }   else{       if(nopponentsdealt == 1){           int pos[2] = {{0},{-2}};            return pos[dealposition-1];         }       if(nopponentsdealt == 2){           int pos[3] = {{-2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 3){           int pos[4] = {{-2},{2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 4){           int pos[5] = {{-2},{3},{2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 5){           int pos[6] = {{-2},{4},{3},{2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 6){           int pos[7] = {{-2},{5},{4},{3},{2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 7){           int pos[8] = {{-2},{6},{5},{4},{3},{2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 8){           int pos[9] = {{-2},{7},{6},{5},{4},{3},{2},{1},{0}};            return pos[dealposition-1];         }   }
        return 0;
    }

As you can see we also need new variables nplayersdealt, dealposition
defined locally and SB should be defined in OH-globalvars.h as bool SB.

### Money and Player Variables

We will need the following variables:

    bool isminbet; int pf_entered; //how many players entered the pot, including dead money posters int nopponentscalling; //how many opponents called a raise double pot; double call; double currentbet; double ncurrentbet; //maximum currentbet on the table double balance; double stack_depth; //the amount of money which is the max(userbalance, highest balance of opponent still playing)

    bool is_playing(int chair){     /*      returns if player is playing at specified chair     */  bitset<16> lok_play((int)gws("playersplayingbits"));
        return lok_play.test(chair);         } double set_ncurrentbet(){    /*      returns the maximum currentbet on the table     */  double dumpvar = 0;
        for(int i = 0; i < 10;i++){         if(is_playing(i) && m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet > dumpvar){           dumpvar = m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet;        }   }
        return dumpvar; } int set_nopponentsbetting(){  /*      number of opponents betting     */  int lok_count = 0;
        for(int k=0;k<10;k++){      if(is_playing(k) && m_holdem_state[(m_ndx)&0xff].m_player[k].m_currentbet > 0 && k != userchair){           lok_count++;        }   }
        return lok_count;
    } int set_nopponentscalling(){  /*      number of opponents betting     */  int lok_count = 0;
        for(int k=0;k<10;k++){      if(is_playing(k) && m_holdem_state[(m_ndx)&0xff].m_player[k].m_currentbet > ncurrentbet-0.01        && k != userchair && m_holdem_state[(m_ndx)&0xff].m_player[k].m_currentbet < ncurrentbet+0.01){             lok_count++;        }   }
        if(lok_count-1 > 0){        return lok_count-1;     }
        return 0;
    } int set_pf_entered(){     /*      returns amout of players who entered the pot    */  if(SB){         if(pf_my_pos == -2){            return nopponentsbetting;       }       if(pf_my_pos== -1){             return nopponentsbetting-1;         }       if(pf_my_pos> -1){          return nopponentsbetting-2;         }   }   else{       if(pf_my_pos== -2){             return nopponentsbetting;       }       if(pf_my_pos> -1){          return nopponentsbetting-1;         }   }
        return 0;
    } double set_stack_depth(){     /*      returns the max(userbalance , oppbalance)   */  double dumpvar = 0;
        for(int i = 0; i < 10;i++){         if(is_playing(i) && m_holdem_state[(m_ndx)&0xff].m_player[i].m_balance+m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet-currentbet > dumpvar                  && i != userchair){          dumpvar = m_holdem_state[(m_ndx)&0xff].m_player[i].m_balance+m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet-currentbet;      }   }   if(balance > dumpvar){      return balance;     }
        return dumpvar;
    }

The functions above will set some of these values the rest we get with
the gws() call. There were only changes made to OH-general.h and
OH-globalvars.h so far, they look like this now:

### OH-globalvars.h

    ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////// //OWN VARS /////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
    //GENERAL VARS bool SB; int userchair; int ismyturn; int betround; double bblind; double decision;
    //MONEY VARS double pot; double call; double currentbet; double ncurrentbet; //maximum currentbet on the table double balance; double stack_depth;
    //OPPONENT VARS int nopponentsbetting; int nopponentscalling; //how many opponents called a raise
    //PREFLOP int pf_my_pos; int pf_entered;
    //SUIT VARS int flushrank;
    //HAND VARS bool issuited; int card[7][2];      //rank 2 ; ... ; 14=A suit 1=C ; 2=D ; 3=H ; 4=S int rankhiplayer; int rankloplayer; int gap;            //0 = pair, 1 = connected, 2 = one-gapper etc...

### OH-general.h

    ////////////////////////////////////////////////////////////////////////// //OWN FUNCS ////////////////////////////////////////////////////////////////////////// int set_flush_rank(){
        bitset<16> srb( (int)gws("srankbits") ); 
        srb = (~srb ) >>= (int)gws("srankhiplayer"); 
        return (int)srb.count();  } void set_card_values(){     /*      sets the card values    */
        //pocket cards  card[0][0] = RANK(m_holdem_state[m_ndx].m_player[userchair].m_cards[0]);    card[0][1] = SUIT(m_holdem_state[m_ndx].m_player[userchair].m_cards[0]);    card[1][0] = RANK(m_holdem_state[m_ndx].m_player[userchair].m_cards[1]);    card[1][1] = SUIT(m_holdem_state[m_ndx].m_player[userchair].m_cards[1]);
        //board cards   for(int u=0;u<5;u++){       card[u+2][0] = RANK(m_holdem_state[(m_ndx)&0xff].m_cards[u]);       card[u+2][1] = SUIT(m_holdem_state[(m_ndx)&0xff].m_cards[u]);   } } int set_rankhiplayer(){     /*      returns hi player rank  */  if(card[0][0] > card[1][0]){        return card[0][0];  }   else{       return card[1][0];  } } int set_rankloplayer(){     /*      returns lo player rank  */  if(card[0][0] > card[1][0]){        return card[1][0];  }   else{       return card[0][0];  } } bool set_suited(){  /*      1 - suited ; 0 -unsuited    */  if(card[0][1] == card[1][1]){       return 1;   }   else{       return 0;   } } int set_gap(){  /*      0 - pair ; 1 - connected ; 2 - one-gapper ...   */  return rankhiplayer-rankloplayer; } bool set_SB(){  /*      checks if sblind was posted at the table    */  for(int i=0;i<10;i++){      if(m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet < bblind-0.01){            return 1;       }   }
        return 0;
    } int pf_pos(){     /*      returns the current position    */  int dealposition = (int)gws("dealposition");    int nopponentsdealt = (int)gws("nopponentsdealt");
        if(SB){         if(nopponentsdealt == 1){           int pos[2] = {{-2},{-1}};           return pos[dealposition-1];         }       if(nopponentsdealt == 2){           int pos[3] = {{-1},{-2},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 3){           int pos[4] = {{-1},{-2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 4){           int pos[5] = {{-1},{-2},{2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 5){           int pos[6] = {{-1},{-2},{3},{2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 6){           int pos[7] = {{-1},{-2},{4},{3},{2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 7){           int pos[8] = {{-1},{-2},{5},{4},{3},{2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 8){           int pos[9] = {{-1},{-2},{6},{5},{4},{3},{2},{1},{0}};           return pos[dealposition-1];         }       if(nopponentsdealt == 9){           int pos[10] = {{-1},{-2},{7},{6},{5},{4},{3},{2},{1},{0}};          return pos[dealposition-1];         }   }   else{       if(nopponentsdealt == 1){           int pos[2] = {{0},{-2}};            return pos[dealposition-1];         }       if(nopponentsdealt == 2){           int pos[3] = {{-2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 3){           int pos[4] = {{-2},{2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 4){           int pos[5] = {{-2},{3},{2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 5){           int pos[6] = {{-2},{4},{3},{2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 6){           int pos[7] = {{-2},{5},{4},{3},{2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 7){           int pos[8] = {{-2},{6},{5},{4},{3},{2},{1},{0}};            return pos[dealposition-1];         }       if(nopponentsdealt == 8){           int pos[9] = {{-2},{7},{6},{5},{4},{3},{2},{1},{0}};            return pos[dealposition-1];         }   }
        return 0;
    } bool is_playing(int chair){   /*      returns if player is playing at specified chair     */  bitset<16> lok_play((int)gws("playersplayingbits"));
        return lok_play.test(chair);         } double set_ncurrentbet(){    /*      returns the maximum currentbet on the table     */  double dumpvar = 0;
        for(int i = 0; i < 10;i++){         if(is_playing(i) && m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet > dumpvar){           dumpvar = m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet;        }   }
        return dumpvar; } int set_nopponentsbetting(){  /*      number of opponents betting     */  int lok_count = 0;
        for(int k=0;k<10;k++){      if(is_playing(k) && m_holdem_state[(m_ndx)&0xff].m_player[k].m_currentbet > 0 && k != userchair){           lok_count++;        }   }
        return lok_count;
    } int set_nopponentscalling(){  /*      number of opponents betting     */  int lok_count = 0;
        for(int k=0;k<10;k++){      if(is_playing(k) && m_holdem_state[(m_ndx)&0xff].m_player[k].m_currentbet > ncurrentbet-0.01        && k != userchair && m_holdem_state[(m_ndx)&0xff].m_player[k].m_currentbet < ncurrentbet+0.01){             lok_count++;        }   }
        if(lok_count-1 > 0){        return lok_count-1;     }
        return 0;
    } int set_pf_entered(){     /*      returns amout of players who entered the pot    */  if(SB){         if(pf_my_pos == -2){            return nopponentsbetting;       }       if(pf_my_pos== -1){             return nopponentsbetting-1;         }       if(pf_my_pos> -1){          return nopponentsbetting-2;         }   }   else{       if(pf_my_pos== -2){             return nopponentsbetting;       }       if(pf_my_pos> -1){          return nopponentsbetting-1;         }   }
        return 0;
    } double set_stack_depth(){     /*      returns the max(userbalance , raiserbalance)    */  double dumpvar = 0;
        for(int i = 0; i < 10;i++){         if(is_playing(i) && m_holdem_state[(m_ndx)&0xff].m_player[i].m_balance+m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet-currentbet > dumpvar       && i != userchair && m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet > ncurrentbet-0.01       && m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet < ncurrentbet+0.01){           dumpvar = m_holdem_state[(m_ndx)&0xff].m_player[i].m_balance+m_holdem_state[(m_ndx)&0xff].m_player[i].m_currentbet-currentbet;      }   }   if(balance > dumpvar){      return balance;     }
        return dumpvar;
    } void int_oh_symbols(){
        //GENERAL VARS  SB = set_SB();  ismyturn = (int)gws("ismyturn");    betround = (int)gws("betround");    userchair = (int)gws("userchair");  bblind = gws("bblind");
        //MONEY VARS    pot = gws("pot");   currentbet = m_holdem_state[(m_ndx)&0xff].m_player[userchair].m_currentbet;     ncurrentbet = set_ncurrentbet();    call = ncurrentbet-currentbet;  balance = m_holdem_state[(m_ndx)&0xff].m_player[userchair].m_balance;   stack_depth = set_stack_depth();
        //OPPONENT VARS     nopponentsbetting = set_nopponentsbetting();    nopponentscalling = set_nopponentscalling();
        //preflop   pf_my_pos = pf_pos();   pf_entered = set_pf_entered();
        //updating card values  set_card_values();
        //updating hand values  issuited = set_suited();    rankhiplayer = set_rankloplayer();  rankloplayer = set_rankhiplayer();  gap = set_gap();
        //updating flush rank   flushrank = set_flush_rank();
    }

## Remarks on Preflop Code

I will distinguish between the following scenarios:

- first in

- limpers/minraise

- raised pot lower than 6bb

- rest

I also define the following standart bet amounts:

        double standart_raise = (pf_entered+4)*bblind+0.01;     double standart_minbet_raise = (2*nopponentscalling+(pf_entered-nopponentscalling)+6)*bblind+0.01;  double standart_Xbet = (3+nopponentscalling)*(call+currentbet);     double dothecall = 0.01; //will call if amount returned by dll > 0

e.g:

- 2 limpers -\> raise = 6BB

- 1 minbettor -\> raise = 7BB

- 4BB raise -\> raise = 12BB

### Source Code

    double preflop(){   /*      preflop code    */
        double standart_raise = (pf_entered+4)*bblind+0.01;     double standart_minbet_raise = (2*nopponentscalling+(pf_entered-nopponentscalling)+6)*bblind+0.01;  double standart_Xbet = (3+nopponentscalling)*(call+currentbet);     double dothecall = 0.01;
        ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////     //FIRST IN  ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////     if(currentbet < sblind+0.01 && pot < bblind+sblind+0.01){       ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////         //BETTING STANDARTS:        //      //+7 - UTG      R TT+, AQ+      //+6 - UTG+1    R 99+, AQ+      //+5 - UTG+2    R 88+, AQ+, AJs+, KQs       //+4 - MP1      R 66+, AQ+, AJs+, KQs       //+3 - MP2      R 22+, AJ+, KQ      //+2 - MP3      R 22+, AT+, KJ+         //+1 - CO       R 22+, A8+, AXs, XT+, 54s+      //+0 - BU       R 22+, AX+, KXs, K7+, Q5s+, Q8+, 97s+, X9+      //-1 - SB       R 22+, A8+, AXs, JT+, 54s+      ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////         if(gap == 0 && rankloplayer > 9         || gap == 0 && rankloplayer > 8 && pf_my_pos < 7        || gap == 0 && rankloplayer > 7 && pf_my_pos < 6        || gap == 0 && rankloplayer > 5 && pf_my_pos < 5        || gap == 0 && pf_my_pos < 4        || rankhiplayer == 14 && rankloplayer > 11      || rankhiplayer == 14 && rankloplayer > 10 && issuited && pf_my_pos < 6         || rankhiplayer == 14 && rankloplayer > 10 && pf_my_pos < 4         || rankhiplayer == 14 && rankloplayer > 9 && pf_my_pos < 3      || rankhiplayer == 14 && rankloplayer > 7 && pf_my_pos < 2      || rankhiplayer == 14 && issuited && pf_my_pos < 2      || rankhiplayer == 14 && pf_my_pos == 0         || rankhiplayer == 13 && rankloplayer == 12 && issuited && pf_my_pos < 6        || rankhiplayer == 13 && rankloplayer == 12 && pf_my_pos < 4        || rankhiplayer == 13 && rankloplayer > 10 && pf_my_pos < 3         || rankhiplayer == 13 && rankloplayer > 6 && pf_my_pos == 0         || rankhiplayer == 13 && issuited && pf_my_pos == 0         || rankhiplayer == 12 && rankloplayer > 7 && pf_my_pos == 0         || rankhiplayer == 12 && rankloplayer > 4 && issuited && pf_my_pos == 0         || rankhiplayer == 11 && rankloplayer > 8 && pf_my_pos == 0         || rankhiplayer > 8 && rankloplayer > 6 && issuited && pf_my_pos == 0       || rankloplayer > 9 && pf_my_pos < 2        || rankloplayer > 8 && pf_my_pos == 0       || rankloplayer > 3 && gap == 1 && issuited && pf_my_pos < 2        ){          return standart_raise;      }   }   ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////     //LIMPERS   ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////     if(ncurrentbet < bblind+0.01 && pot > bblind+sblind+0.01){      ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////         //BETTING STANDARTS:        //      //+6 - UTG+1    R TT+, AQ+ C 22+, ATs+, KJs         //+5 - UTG+2    R TT+, AQ+ C 22+, ATs+, KJs         //+4 - MP1      R TT+, AQ+, AJs+, KQs C 22+, ATs+, KJs C2 JTs+      //+3 - MP2      R TT+, AQ+, AJs+, KQs C 22+, ATs+, KJs C2 JTs+      //+2 - MP3      R TT+, AQ+, AJs+, KQs C 22+, ATs+, KJs C2 54s+      //+1 - CO       R TT+, AQ+, AJs+, KQs C 22+, AXs, JTs+, 54s+ R2 22+, A8s+, JT+, 54s+ C2 54o+, 53+       //+0 - BU       R TT+, AQ+, AJs+, KQs C 22+, AXs, JTs+, 54s+ R2 22+, AXs, J8s+, JT+, 54s+ C2 54o+, 53+      //-1 - SB       R TT+, AQ+, AJs+, KQs C 22+, AXs, JTs+, 54s+ C2 XXs, 54o+, 53+      //-2 - BB       R TT+, AQ+, AJs+, KQs       ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////         if(gap == 0 && rankloplayer >= 10       || gap == 0 && pf_entered < 3 && pf_my_pos > -1 && pf_my_pos < 2        || rankhiplayer == 14 && rankloplayer > 11      || rankhiplayer == 14 && rankloplayer > 10 && issuited && pf_my_pos < 5         || rankhiplayer == 14 && rankloplayer > 9 && pf_entered < 3 && pf_my_pos > -1 && pf_my_pos < 2      || rankhiplayer == 14 && rankloplayer > 7 && issuited && pf_entered < 3 && pf_my_pos > -1 && pf_my_pos < 2      || rankhiplayer == 14 && issuited && pf_entered < 3 && pf_my_pos == 0       || rankhiplayer == 13 && rankloplayer == 12 && issuited && pf_my_pos < 5        || rankloplayer > 9 && pf_entered < 3 && pf_my_pos > -1 && pf_my_pos < 2        || rankloplayer > 7 && issuited && pf_entered < 3 && pf_my_pos == 0         || rankloplayer > 3 && gap == 1 && issuited && pf_entered < 3 && pf_my_pos > -1 && pf_my_pos < 3        ){          return standart_raise;      }       if(gap == 0             || rankhiplayer == 14 && rankloplayer > 9 && issuited           || rankhiplayer == 14 && issuited && pf_my_pos < 2          || rankhiplayer == 13 && rankloplayer > 10 && issuited          || rankloplayer > 9 && issuited && pf_entered > 1 && pf_my_pos < 5          || rankloplayer > 9 && issuited && pf_my_pos < 2            || rankloplayer > 3 && gap == 1 && issuited && pf_my_pos < 2            || rankloplayer > 3 && gap == 1 && pf_entered > 1 && pf_my_pos < 3          || gap == 2 && pf_entered > 1 && pf_my_pos < 3          || issuited && pf_entered > 1 && pf_my_pos == -1            ){              return dothecall;       }   }   ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////     //MINRAISE  ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////     if(ncurrentbet > bblind+0.01 && ncurrentbet < 2*bblind+0.01 && pot > bblind+sblind+0.01){       ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////         //BETTING STANDARTS:        //      //+6 - UTG+1    R TT+, AQ+ C 22+, AJs, KQs      //+5 - UTG+2    R TT+, AQ+ C 22+, AJs, KQs      //+4 - MP1      R TT+, AQ+ C 22+, AJs, KQs C2 JTs+      //+3 - MP2      R TT+, AQ+ C 22+, AJs, KQs C2 JTs+      //+2 - MP3      R TT+, AQ+ C 22+, AJs, KQs C2 JTs+      //+1 - CO       R TT+, AQ+ C 22+, AXs, JTs+         //+0 - BU       R TT+, AQ+ C 22+, AXs, JTs+         //-1 - SB       R TT+, AQ+ C 22+, AJs, KQs      //-2 - BB       R TT+, AQ+ C 22+, AJs, KQs      ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////                     if(gap == 0 && rankloplayer > 9         || rankhiplayer == 14 && rankloplayer > 11      ){              return standart_minbet_raise;       }       if(gap == 0         || rankhiplayer == 14 && rankloplayer > 10 && issuited      || rankhiplayer == 13 && rankloplayer == 12 && issuited         || rankloplayer > 9 && issuited && pf_entered > 1 && pf_my_pos > -1 && pf_my_pos < 5        || rankhiplayer == 14 && issuited && pf_my_pos > -1 && pf_my_pos < 2        || rankloplayer > 9 && issuited && pf_my_pos > -1 && pf_my_pos < 2      ){              return dothecall;       }   }   ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////     //RAISED POT 6*BB-  ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////     if(currentbet < 0.01 && call > 2*bblind+0.01 && call < 6*bblind+0.01    || currentbet < sblind+0.01 && pf_my_pos == -1 && call > 2*bblind-sblind+0.01 && call < 6*bblind-sblind+0.01    || currentbet < bblind+0.01 && pf_my_pos == -2 && call > bblind+0.01 && call < 5*bblind+0.01){      ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////         //BETTING STANDARTS:        //      //  R TT+, AK       ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////         if(gap == 0 && rankloplayer > 9         || rankhiplayer == 14 && rankloplayer == 13         ){          return standart_Xbet;       }   }   ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////     //REST  ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////         ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////         //BETTING STANDARTS:        //      //  R KK+       //  C 22+           (at least 1:20 (stack+pot-call)/call ratio of the raising chair)        //  C 54s+ and JTs+ (at least 1:20 (stack+pot-call)/call ratio of the raising chair) and 1 caller+      //  C AXs           (at least 1:20 (stack+pot-call)/call ratio of the raising chair) and 2 caller+      ///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////         if(gap == 0 && rankloplayer > 12){          return standart_Xbet;       }       if(gap == 0 && (stack_depth+pot-call)/call > 19         || rankloplayer > 3 && gap == 1 && issuited && (stack_depth+pot-call)/call > 19 && nopponentscalling > 0        || issuited && rankhiplayer == 14 && (stack_depth+pot-call)/call > 19 && nopponentscalling > 1      ){          return dothecall;       }
        return 0;
    }

I think it’s pretty much self explaining. If you have any questions
please ask in the forum thread. The code itself is untested in this
form, all I know is that it compiles, but there has not been any bug
testing and I don’t know if all values are set as they are supposed to,
if you find any bugs please report.

## Files

The files for this tutorial can be found here:
<http://www.maxinmontreal.com/wiki/index.php5?title=Image:OH-DLL-D.rar>.
!!!
