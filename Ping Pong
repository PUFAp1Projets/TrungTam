#include <iostream>
#include <cstdlib>
#include <SDL/SDL.h>
#include <SDL/SDL_image.h>
#include <SDL/SDL_ttf.h>
#include <sstream>

using namespace std;

const int SCREEN_HEIGHT = 400;
const int SCREEN_WIDTH = 640;
const int SCREEN_BPP = 32;

const int TAILLE_BALL = 25;
const int TAILLE_BAR_W = 25;
const int TAILLE_BAR_H = 98;
const int TAILLE_BUTTON_H = 60;
const int TAILLE_BUTTON_W = 150;

SDL_Surface* screen;
SDL_Event event;

bool quit = false;           
bool play = false;
bool instruct = false;
bool intro = true;
bool paused = false;
bool replay = false;
bool win_1 = false;
bool win_2 = false;

//Pour jouer
//*******************************
void intro_game()   // enter menu
{
  intro = true ;
  instruct = false ;
  play = false ;
  quit = false ;
  SDL_FreeSurface(screen);
}

void inst_game()   // enter instruction
{
  instruct = true;
  play = false;
  quit = false;
  intro = false;
  SDL_FreeSurface(screen);
}

void play_game()   // enter playgame
{
  play = true;
  quit = false;
  intro = false;
  instruct =false;
  SDL_FreeSurface(screen);
}

void pause_game()   // to pause game
{
  if(!paused){
    paused = true;
  }
  else
    paused = false;  
  SDL_FreeSurface(screen);
}

void quit_game()   // quit game
{
  quit = true;
  play = false;
  intro = false;
  instruct = false;
}
//************************************


//Fonctions pour les images , les texts
//*****************************************************************************************
//load Image
SDL_Surface *loadImage(string filename ) 
{
  return IMG_Load(filename.c_str());
}

//load Image With Color Key
SDL_Surface *loadImageWithColorKey(string filename, int r, int g, int b) 
{
    SDL_Surface* optimizedImage;
    optimizedImage = IMG_Load( filename.c_str());
    SDL_SetColorKey(optimizedImage, SDL_SRCCOLORKEY, 
            SDL_MapRGB(optimizedImage->format, r, g, b));
    return optimizedImage;
}
 
// poser les images sur ecran
void applySurface(int x, int y, SDL_Surface* source, 
		  SDL_Surface* destination, SDL_Rect* clip = NULL)
{
    SDL_Rect offset;
    offset.x = x;
    offset.y = y;
    SDL_BlitSurface( source, clip, destination, &offset );
}

//*** Messages ***
void showMessageScreen(string message,int x,int y,
          TTF_Font *font,int fontSize,SDL_Color textColor,SDL_Surface* &screen)
{
  string mot="";
  string space=" ";
  int i=0,j;
  SDL_Surface *mes=NULL;

  j = message.find(space);
  while( j != string::npos )
    {
      mot=message.substr(i,j-i);
      if(mot != "")
    {
      mes=TTF_RenderText_Solid(font,mot.c_str(),textColor);
      applySurface(x,y,mes,screen,NULL);
      x+=mes->w;
    }
      x+=fontSize;
      i=j+1;
      j = message.find(space,i);
    }
      
  mot=message.substr(i);
  mes=TTF_RenderText_Solid(font,mot.c_str(),textColor);
  applySurface(x,y,mes,screen,NULL);
}
//******************************************************************


// ******************** LES IMAGES *********************************************
SDL_Surface* fond = loadImage("Images/screen.bmp");         //background in game
SDL_Surface* title = loadImage("Images/screen.bmp");       //background in menu
SDL_Surface* button = loadImage("Images/button.bmp");     //button in menu  
SDL_Surface* loser = IMG_Load("Images/loser.png");       // lose/win images 
SDL_Surface* winner = IMG_Load("Images/winner.png");
SDL_Surface* main_menu = loadImage("Images/Menu.png");    // after win
SDL_Surface* player01 = IMG_Load("Images/gallet1.png");  // les bar pour joueur 1 ou2
SDL_Surface* player02 = IMG_Load("Images/gallet2.png");
SDL_Surface* ball = IMG_Load("Images/ball.png");  // ball
SDL_Surface* instruction = loadImageWithColorKey( "Images/instruction.bmp", 255,255,255);   // button in menu
//********************************************************************************


//Structure pour les images en mouvement
struct ImageMove
{
  int x;     // abscisse du centre de la balle
  int y;     // ordonnée du centre de la balle
  int w;     // largeur de la balle
  int h;     // hauteur de la balle
  int mvt_x; // mouvement sur l'axe des abscisses
  int mvt_y; // mouvement sur l'axe des ordonnées
  SDL_Surface *source;
};

//Image "ball.bmp"
void initBall (ImageMove &b)
{
  b.x = SCREEN_WIDTH/2 - TAILLE_BALL/2;
  b.y = SCREEN_HEIGHT/2 - TAILLE_BALL/2;
  b.w = TAILLE_BALL;
  b.h = TAILLE_BALL;
  b.mvt_x= 2;  // Modifier pour tester
  b.mvt_y= 2;
  
  b.source = ball;
}

//Image "player01.bmp"
void initPlayer01 (ImageMove &b)
{
  b.x = 30;
  b.y = SCREEN_HEIGHT/2 - TAILLE_BAR_H/2;
  b.w = TAILLE_BAR_W;
  b.h = TAILLE_BAR_H;
  b.mvt_x = 0;
  b.mvt_y = 5;
  b.source = player01;
} 
  
//Image "player02.bmp"
void initPlayer02 (ImageMove &b)
{
  b.x = SCREEN_WIDTH - TAILLE_BAR_W - 30;
  b.y = SCREEN_HEIGHT/2 - TAILLE_BAR_H/2;
  b.w = TAILLE_BAR_W;
  b.h = TAILLE_BAR_H;
  b.mvt_x = 0;
  b.mvt_y = 5;
  b.source = player02;
} 


//********************** Le mouvement des objet *************************
//***********************************************************************

//Collision des images
bool collision(SDL_Rect a, SDL_Rect b)
{
  int LeftA , LeftB , RightA , RightB;
  int TopA , TopB , BottomA , BottomB;
  
  LeftA = a.x; RightA = a.x + a.w ; TopA = a.y ; BottomA = a.y + a.h;
  LeftB = b.x; RightB = b.x + b.w ; TopB = b.y ; BottomB = b.y + b.h;
  
  if (LeftA >= RightB)
    return false;
  if (LeftB >= RightA)
    return false;
  if (TopA >= BottomB)
    return false;
  if (TopB >= BottomA)
    return false;
  
  return true;
}

//Random direction for ball
void direction(ImageMove &b)
{
  int a = rand()%100;  // randomize a number 0~99
  switch (a%4)
    {
    case 1:  // UP + LEFT
      b.mvt_x *= -1;
      b.mvt_y *= -1;
      break;
    case 2:  // DOWN + LEFT
      b.mvt_x *= -1;
      break;
    case 3:  // UP + RIGHT
      b.mvt_y *= -1;
      break;
    case 0: // DOWN + RIGHT
      break;
    }
}

//move ball
void moveBall (ImageMove &b, SDL_Rect wall1, SDL_Rect wall2)
{
  b.x += b.mvt_x;  //Mouvement horizontal

  SDL_Rect tmp;
  tmp.x=b.x;
  tmp.y=b.y;
  tmp.h=TAILLE_BALL;
  tmp.w=TAILLE_BALL;  
  // Correction Mouvement Horizontal
  if( collision(tmp,wall1) || collision(tmp,wall2) )
    {
      b.x -= b.mvt_x;
      b.mvt_x *= -1;
    }
    
  b.y += b.mvt_y;   //Mouvement vertical

  tmp.x=b.x;
  tmp.y=b.y;
  // Correction Mouvement Vertical
  if((b.y+TAILLE_BALL>SCREEN_HEIGHT) || (b.y-TAILLE_BALL<0) || collision(tmp,wall1) || collision(tmp,wall2))
    {
      b.y -= b.mvt_y;
      b.mvt_y *= -1;
    } 
}

// Move ball faster every 15 s.
void faster(ImageMove &b, int &startTicks)  
{
  // if NOW - STARTpoint = 16s -> faster
  if ( (SDL_GetTicks() - startTicks) /1000 > 16 && b.x < SCREEN_WIDTH/2 + 2  && b.x > SCREEN_WIDTH/2 - 2)  
    {
      if(b.mvt_x>0)
	b.mvt_x += 1;
      else
	b.mvt_x -=1;
      if(b.mvt_y>0)
	b.mvt_y += 1;
      else
	b.mvt_y -= 1;

      startTicks = SDL_GetTicks();  //reset STARTpoint
      
      //Announce Faster
      
      SDL_Color Color = {255,0,0};
      TTF_Font *font;
      string police="Fonts/Blabla.ttf";
      int size = 40;
      font = TTF_OpenFont(police.c_str(),size);
      showMessageScreen("Faster!!!", 225, 150, font , size , Color , screen);
      SDL_Flip(screen);
      SDL_Delay (300);
      TTF_CloseFont(font);  
      
    }
}

//show ImageMove
void showImageMouve(ImageMove b, SDL_Surface *s)
{
  applySurface(b.x,b.y,b.source,s);
}

//Stop move for two bars
void stopMove(ImageMove &b, ImageMove &a)
{
  //Keep the 2 bars inside the screen
  if(b.y<0)
    b.y = 0;
  else if (b.y > SCREEN_HEIGHT - TAILLE_BAR_H)
    b.y = SCREEN_HEIGHT- TAILLE_BAR_H;

  /*  Faire gliser la bar si bar + ball confondu
  SDL_Rect ball,bar;
    
  ball.x=a.x;
  ball.y=a.y;
  ball.h=TAILLE_BALL;
  ball.w=TAILLE_BALL;

  bar.x = b.x;
  bar.y = b.y;
  bar.w = TAILLE_BAR_W;
  bar.h = TAILLE_BAR_H;
   
  if( collision(ball,bar) )
    {
      if (a.y <= b.y + TAILLE_BAR_H/2 || a.y-b.y <= b.y)
	b.y = a.y + TAILLE_BALL;
      else
	b.y = a.y - TAILLE_BAR_H;
    }
  */     
}
    
//Control player 1 --- KEY W + KEY S
void control01(ImageMove &b, ImageMove a)
{ 
  Uint8 *keystates = SDL_GetKeyState( NULL );
    
  if( keystates[ SDLK_w ] )
    b.y-=b.mvt_y;
  else if( keystates[ SDLK_s ] )
    b.y+=b.mvt_y;
  stopMove(b,a);
}   
    
//Control player 2 --- KEY UP + KEY DOWN
void control02(ImageMove &b, ImageMove a)
{
  Uint8 *keystates = SDL_GetKeyState( NULL );
  if( keystates[ SDLK_UP ] )
    b.y-=b.mvt_y;
  else if( keystates[ SDLK_DOWN ] )
    b.y+=b.mvt_y;
  stopMove(b,a);
}
//*************************** Finir Mouvement ************************** 
//**********************************************************************


//**** la ligne au milieu ****
void ligneMilieu()
{
  SDL_Rect ligne;
  ligne.x = 320;
  ligne.w = 1;
  ligne.h = 30;
  
  for ( int i = 0 ; i < SCREEN_HEIGHT ; i += 60 )
    {
      ligne.y = i+10; 
      SDL_FillRect ( screen , &ligne ,
		     SDL_MapRGB ( screen -> format ,255,0,0));
    }
}
		    

// ************* Introduction *************
void Introduction()
{  
  while (intro)
    {
      SDL_Rect button_quit1,button_play1, button_quit2, button_play2;
      //Quit button RED
      button_quit1.x = 0;
      button_quit1.y = 0;
      button_quit1.h = TAILLE_BUTTON_H;
      button_quit1.w = TAILLE_BUTTON_W;
      //Play button RED
      button_play1.x = 0;
      button_play1.y = 100;
      button_play1.h = TAILLE_BUTTON_H;
      button_play1.w = TAILLE_BUTTON_W;
      //Quit button YELLOW (when clicked)
      button_quit2.x = 200;
      button_quit2.y = 0;
      button_quit2.h = TAILLE_BUTTON_H;
      button_quit2.w = TAILLE_BUTTON_W;
      //Play button YELLOW (when clicked)
      button_play2.x = 200;
      button_play2.y = 100;
      button_play2.h = TAILLE_BUTTON_H;
      button_play2.w = TAILLE_BUTTON_W;
      
      //The buttons position
      int pos_play_x = 150;
      int pos_play_y = 230;
      int pos_quit_x = 350;
      int pos_quit_y = 230;

      int pos_inst_x = 220;
      int pos_inst_y = 300;

      applySurface (0,0,title,screen);
      applySurface (pos_inst_x, pos_inst_y, instruction,screen ,NULL);
      applySurface (pos_play_x,pos_play_y,button,screen,&button_play1); 
      applySurface (pos_quit_x,pos_quit_y,button,screen,&button_quit1);
      SDL_Flip (screen);
      
      //Check event
      while(SDL_PollEvent(&event))
	if (event.type == SDL_QUIT)
	  {
	    quit_game();
	  }

        else if( event.type == SDL_MOUSEBUTTONDOWN )
	  {
	    //les coordonnées de la souris
	    int x = event.button.x;
	    int y = event.button.y;
	    
	    // QUIT game
	    if( ( x > pos_quit_x ) && 
		( x < pos_quit_x + button_quit1.w )  &&
		( y > pos_quit_y  ) && 
		( y < pos_quit_y + button_quit1.h )  )
	      {
		if(event.button.button == SDL_BUTTON_LEFT)
		  {
		     applySurface (pos_quit_x,pos_quit_y,button,screen,&button_quit2);
		     SDL_Flip (screen);
		     SDL_Delay(50);
		     quit_game();
		  }
	      }
	    //PLAY game
	    if (  ( x > pos_play_x ) && 
		  ( x < pos_play_x + button_play1.w ) &&
		  ( y > pos_play_y  ) && 
		  ( y < pos_play_y + button_play1.h ) ) 
	      {
		if(event.button.button == SDL_BUTTON_LEFT)
		  {
		    applySurface (pos_play_x,pos_play_y,button,screen,&button_play2);
		    SDL_Flip (screen);
		    SDL_Delay(50);
		    play_game();
		  }  
	      }
	    //HELP game
	    if (  ( x > pos_inst_x + 10 ) && 
		  ( x < pos_inst_x + 200 -10 ) &&
		  ( y > pos_inst_y + 8 ) && 
		  ( y < pos_inst_y + 100 -8 ) ) 
	      {
		if(event.button.button == SDL_BUTTON_LEFT)
		  inst_game();
	      }
	  }
      SDL_Delay (100);
    }    
}

//********* After Introduction **********
//Counting 3 to 1 ... Start
void afterIntro()
{
  applySurface(0,0,fond,screen);

  SDL_Color Color = {255,0,0};
  TTF_Font *font;
  string police="Fonts/Abril.ttf";
  int size = 50;

  font = TTF_OpenFont(police.c_str(),size);
  showMessageScreen ( " Start in " , 165 , 150 , font , size , Color , screen);
  SDL_Flip(screen);
  SDL_Delay (500);
  TTF_CloseFont(font);

  applySurface(0,0,fond,screen);
  size += 40;
  font = TTF_OpenFont(police.c_str(),size);
  showMessageScreen ( " 3 " , 180 , 150 , font , size , Color , screen);
  SDL_Flip(screen);
  SDL_Delay (900);
  TTF_CloseFont(font);

  applySurface(0,0,fond,screen);
  size += 40;
  font = TTF_OpenFont(police.c_str(),size);
  showMessageScreen ( " 2 " , 130 , 150 , font , size , Color , screen);
  SDL_Flip(screen);
  SDL_Delay (900);
  
  applySurface(0,0,fond,screen);
  size += 40;
  font = TTF_OpenFont(police.c_str(),size);
  showMessageScreen ( " 1 " , 100 , 150 , font , size , Color , screen);
  SDL_Flip(screen);
  SDL_Delay (1000);
  TTF_CloseFont(font);
}

//********* Instruction *************
void Help()
{
  while (instruct)
    {
      applySurface (0,0,fond,screen);   
      ligneMilieu();                    
      applySurface (30 , SCREEN_HEIGHT/2 - TAILLE_BALL/2,
		    player01, screen);  
      applySurface (610 - 11, SCREEN_HEIGHT/2 - TAILLE_BALL/2,
		    player02, screen);   

      TTF_Font *font;
      SDL_Color Color1 = {255,255,0};
      SDL_Color Color2 = {53,255,39};
      SDL_Color Color3 = {226,86,58};
      SDL_Color Color4 = {200,30,170};
   
      int size1 = 45;
      int size2 = 20;
      int size3 = 30;
      string police="Fonts/Acens.ttf";    
      
      font = TTF_OpenFont(police.c_str(),size1);
      showMessageScreen("Player 1" , 80 , 50 , font , size1 , Color1 , screen);
      showMessageScreen("Player 2" , 380 , 50 , font , size1, Color1 , screen);
      TTF_CloseFont(font);
      
      font = TTF_OpenFont(police.c_str(),size3);
      showMessageScreen("Get 5 points to win!", 150 , 10 , font , size3 , Color4, screen);
      TTF_CloseFont(font);

      font = TTF_OpenFont(police.c_str(),size2);
      //Player 1
      showMessageScreen("MOVE UP" , 20 , 120 , font , size2 , Color2, screen); 
      showMessageScreen("MOVE DOWN", 20 , 270 , font , size2, Color2,screen);
      showMessageScreen("press W" , 20 , 150 , font , size2 , Color2, screen); 
      showMessageScreen("press S", 20 , 300 , font , size2, Color2,screen);
      //Player2
      showMessageScreen("MOVE UP" , 500 , 120 , font , size2 , Color2, screen); 
      showMessageScreen("MOVE DOWN", 460 , 270 , font , size2, Color2,screen);
      showMessageScreen("press UP" , 500 , 150 , font , size2 , Color2, screen); 
      showMessageScreen("press DOWN", 460 , 300 , font , size2, Color2,screen);
      //Function key Pause/Unpause in game + Back to menu (blinking)
      showMessageScreen("Press P to Pause or Unpause in game",100 ,335 , font , size2 , Color2 , screen);
      SDL_Flip(screen);
      SDL_Delay(400);
      showMessageScreen("Press M to go back to Main Menu", 105 ,375 , font , size2 , Color3 , screen );
      SDL_Flip(screen);
      SDL_Delay(400);
      TTF_CloseFont(font);

      while(SDL_PollEvent(&event))
	if (event.type == SDL_KEYDOWN)
	  {
	    if(event.key.keysym.sym == SDLK_m)
	      intro_game();
	  }
	else if (event.type == SDL_QUIT)
	  quit_game();
      SDL_Delay (10);
    }
}

void show_paused()
{
  TTF_Font *font;
  SDL_Color Color1 = {0,0,0};
  SDL_Color Color2 = {255,255,255};
  int size1 = 30;
  int size2 = 20;
  string police="Fonts/Acens.ttf";
  //ouverture de la fonte
  font = TTF_OpenFont(police.c_str(),size1);
 
  showMessageScreen("Paused" , 280 , 170 , font , size1 , Color1 , screen);
  TTF_CloseFont(font);

  font = TTF_OpenFont(police.c_str(),size2);
  showMessageScreen("Press P to Unpause" , 220, 210 , font ,size2,Color2,screen);
  showMessageScreen("Press M to Main Menu" , 200, 240 , font ,size2,Color2,screen);
  TTF_CloseFont(font);
  SDL_Flip(screen);
}

// calculate points and write '+1' on screen
void calcul_points(ImageMove b , int &points_player1 , int &points_player2)
{
  TTF_Font *fonts;
  SDL_Color Color1 = {255,255,0};
  int fontSize=50;
  string police="Fonts/Blabla.ttf";
  fonts = TTF_OpenFont(police.c_str(),fontSize);

  if ( b.x > SCREEN_WIDTH)   // si ball touche limit de joueur2
    {
      points_player1 ++;                // un point pour joueur 1
      showMessageScreen("+ 1",100,150,fonts,fontSize,Color1,screen);
      SDL_Flip(screen);
      SDL_Delay ( 1000 );
      replay = true;
    }
  if ( b.x <= -TAILLE_BALL  )  // si ball touche la limit de joueur2 
    {
      points_player2 ++;       // un point pour joueur2
      showMessageScreen("+ 1",420,150,fonts,fontSize,Color1,screen);
      SDL_Flip(screen);
      SDL_Delay ( 1000 );
      replay = true;
    }
  TTF_CloseFont(fonts);
}

//Afficher points de 2 joueurs 
void show_points(int points_player1, int points_player2)
{
  TTF_Font *fonts;
  SDL_Color Color2 = {53,255,39};  // joueur1
  SDL_Color Color3 = {226,86,58};  // joueur2
  int fontSize=40;
  string police="Fonts/Blabla.ttf";
  //ouverture de la fonte
  fonts = TTF_OpenFont(police.c_str(),fontSize);

  ostringstream mssg;
  //Point joueur 1
  mssg.flush();
  mssg.str("");
  mssg <<  points_player1;
  showMessageScreen(mssg.str(),130,20,fonts,fontSize,Color2,screen);
  //Point joueur 2
  mssg.flush();
  mssg.str("");
  mssg << points_player2;
  showMessageScreen(mssg.str(),470, 20,fonts,fontSize,Color3,screen);
  
  TTF_CloseFont(fonts);
}

//Si un joueur gagne un point, on re-commence la jeu.
void replay_game(ImageMove &BALL, ImageMove &BAR1, ImageMove &BAR2 )
{
  initBall(BALL);
  initPlayer01(BAR1);
  initPlayer02(BAR2);
  replay = false;
}

// Pour determiner si un joueur a gagne (avoir 5 points)
void win_game(int points_player1, int points_player2)
{
  if (points_player1==5)
    win_1 = true;
  if (points_player2==5)
    win_2 = true;
}

// Ecran apres un joueur a gagne
void show_win(int points_player1, int points_player2)
{
  int a,b;     // a : for loser --- b : for winner
  a=0;
  b=0;
  //main menu button
  int pos_menu_x = 240;   //position sur screen
  int pos_menu_y = 200;

  SDL_Rect main_menu1,main_menu2;
  main_menu1.x=0;
  main_menu1.y=0;
  main_menu1.w=150;
  main_menu1.h=60;

  main_menu2.x=200;
  main_menu2.y=0;
  main_menu2.w=150;
  main_menu2.h=60;

  if(win_1)
    a = 320;   // player 2 lost 
  else
    b = 320;    // player 1 lost
  
  while ( win_1 || win_2 )
    {
      applySurface(a,0,loser,screen);
      applySurface(b,0,winner,screen);
      
      TTF_Font *font;
      SDL_Color Color1 = {0,0,0};   // les joueurs   
      SDL_Color Color2 = {255,0,0}; //les points
      int size1 = 40;
      int size2 = 50;
      string police="Fonts/Acens.ttf";
      
      //ouverture de la fonte
      font = TTF_OpenFont(police.c_str(),size1);
      showMessageScreen("Player 1" , 80 , 10 , font , size1 , Color1 , screen);
      showMessageScreen("Player 2" , 380 , 10 , font , size1, Color1 , screen);
      TTF_CloseFont(font);
      
      police="Fonts/Blabla.ttf";
      font = TTF_OpenFont(police.c_str(),size2);
      
      ostringstream mssg;//penser à inclure -->  #include <sstream>
      //Player 1 point
      mssg.flush();
      mssg.str("");
      mssg <<  points_player1;
      showMessageScreen(mssg.str(),130,60,font,size2,Color2,screen);
      //Player 2 point
      mssg.flush();
      mssg.str("");
      mssg << points_player2;
      showMessageScreen(mssg.str(),470,60,font,size2,Color2,screen);

      TTF_CloseFont(font);
      //Button menu
      applySurface(pos_menu_x,pos_menu_y,main_menu,screen,&main_menu2);
      SDL_Flip (screen);
      
      while(SDL_PollEvent(&event))
	if (event.type == SDL_QUIT)
	  {
	    quit_game();
	    win_1=false;
	    win_2=false;
	  }
	else if (event.type == SDL_MOUSEBUTTONDOWN )
	  {
	    //les coordonnées de la souris
	    int x = event.button.x;
	    int y = event.button.y;
	    if (  ( x > pos_menu_x ) && 
		  ( x < pos_menu_x + main_menu1.w ) &&
		  ( y > pos_menu_y ) && 
		  ( y < pos_menu_y + main_menu1.h ) ) 
	      {
		if(event.button.button == SDL_BUTTON_LEFT)
		  {
		    applySurface(pos_menu_x,pos_menu_y,main_menu,screen,&main_menu1);
		    SDL_Flip (screen);
		    SDL_Delay(100);
		    intro_game();
		    win_1=false;
		    win_2=false;
		  }
	      }
	    SDL_Delay (10);
	  } 
    }
}


//---------------------------------------------------------------------------
//-------------------------- MAIN PROGRAM -----------------------------------
//---------------------------------------------------------------------------
int main() 
{
  int points_player1;
  int  points_player2;
  int startTicks, pauseTicks;

  ImageMove ballon,bar1,bar2;   //Les images en mouvement
  initBall (ballon);
  initPlayer01 (bar1);
  initPlayer02 (bar2);
  
  SDL_Rect bar1_rect,bar2_rect;    //Les bars de 2 joueurs
  bar1_rect.x = bar1.x;
  bar1_rect.w = bar1.w;
  bar1_rect.h = bar1.h;

  bar2_rect.x = bar2.x;
  bar2_rect.w = bar2.w;
  bar2_rect.h = bar2.h;

  SDL_Init (SDL_INIT_EVERYTHING);
  SDL_WM_SetIcon(loadImage("/Images/Letter P.png"), NULL);
  screen=SDL_SetVideoMode(SCREEN_WIDTH , SCREEN_HEIGHT , 
		   SCREEN_BPP , SDL_SWSURFACE);
  SDL_WM_SetCaption( "Pong - The revival", NULL );

  TTF_Init();
  // Commencer le jeu
  while(!quit) {
    Introduction();   //Menu
    
    Help();           //Instruction
   
    if(play)          //Compter pour preparer
      afterIntro();
    
    points_player1=0; //Initaliser les points
    points_player2=0;
    startTicks = SDL_GetTicks(); // Initialiser timer
    pauseTicks = 0;

    srand(time(NULL));

    while (play) {     
      if(replay) {
		replay_game(ballon,bar1,bar2);
		startTicks = SDL_GetTicks();
		direction(ballon);
      }
      
      applySurface (0,0,fond,screen);    //background
      ligneMilieu();                     // la ligne au milieu
      showImageMouve (ballon,screen);    // la ball
      showImageMouve (bar1,screen);      // joueur1
      showImageMouve (bar2, screen);     // joueur2
      show_points(points_player1,points_player2);  // les points
      
      while(SDL_PollEvent(&event))
		if (event.type == SDL_QUIT)
			quit_game();
		else if(event.type== SDL_KEYDOWN)
			switch(event.key.keysym.sym) 
			{
			case SDLK_p :                  //Si p est touche, le jeu pause
				pause_game();
			break;
			case SDLK_m :                 //Si le jeu pause, touhe m pour la menu. 
			if(paused)
				intro_game();
			break;
			}

      //Pauser
      if (!paused)    //Pas pause
	{    
	  //Modifier position ball 
	  moveBall(ballon, bar1_rect, bar2_rect);  
	  //Pour modifier positions des bars
	  control02(bar2,ballon);
	  control01(bar1,ballon);
	  bar1_rect.y = bar1.y;
	  bar2_rect.y = bar2.y;
	  
	  //Calculer les points
	  calcul_points(ballon, points_player1, points_player2); 
	  
	  startTicks += pauseTicks;  //  Si il y a temps pause
	  pauseTicks = 0;            // Reset pause time
	  //la ball plus rapide chaque 15 secondes
	  faster(ballon,startTicks);
	}
      else    // Si pauser
	{                    
	  pauseTicks = SDL_GetTicks() - startTicks;      // Sauver temps pause
	  show_paused();   
	}

      SDL_Flip(screen);
      SDL_Delay(10);
      win_game(points_player1,points_player2);   // Etudier si qqun a 5 pts  
      show_win(points_player1,points_player2);  
    }
  }

  TTF_Quit();  // Stop TTF
  SDL_FreeSurface(screen);
  SDL_Quit();
  return 0;
}
