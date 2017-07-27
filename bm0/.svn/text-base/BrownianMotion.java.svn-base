/* Brownian motion          */
/* coded by Ikeuchi Mitsuru */
/* ver 0.0.1   2002.07.28   */
/* ver 0.0.2   2003.08.09  flicker noise reduced */
/* ver 0.0.3   2004.07.18  improved control pannel */
/* modified by Fumiyuki Ishii */
/* modified version 0.1 */ 

import java.awt.*;
import java.awt.event.*;
import java.applet.*;

public class BrownianMotion extends Applet
  implements ItemListener, ActionListener, AdjustmentListener, Runnable {

/*-------------------------------------- define gloval -----*/

  Button bt_disp;
  Button reset;
  Scrollbar scr;
  Thread th = null;

  Dimension dim;
  Image imgOff;
  Graphics gOff;

  int sleepTime = 50;
  int atomSizeX =60; 
  int atomSizeY =60;  
  int dispR = 10;         /* display molecular radious */
  int paintSizeX = atomSizeX*dispR; 
  int paintSizeY = atomSizeY*dispR;
  int wakuX = paintSizeX + dispR;
  int wakuY = paintSizeY + dispR;

  int Nmtmax=200+1;              /* number of molecules    */
  int NN = Nmtmax + 1;
  double t = 0.0;              /* time (s)               */
  double dt = 25.0*1.0e-15;    /* time division (s)      */
  double sgm = 3.40e-10;       /* L-J sigma for Ar (m)   */
  double eps = 1.67e-21;       /* L-J epsilon for Ar (J) */
  double mas = 39.95*1.67e-27; /* mass of Ar (kg)        */

  int dispSW = 0;
  int constMoment = 0;
  double contTemp =300;

  double xMax = atomSizeX*sgm; /* x-Box size in (m)      */
  double yMax = atomSizeY*sgm; /* y-Box size in (m)      */
  double zMax = 0.0;           /* z-Box size in (m)      */

  double AU = 1.661e-27;
  double EE = 1.602e-19;
  double KB = 1.38e-23;
  double AA = 1.0e-10;
  double FS = 1.0e-15;
  double Molec[][] = {
  /*         mass         charge   E(in kB)  sigma(A)   dt(s)  */
  /* 0 He*/ {   4.003*AU,  0.0*EE,  10.2*KB, 2.576*AA,  5.0*FS },
  /* 1 Ne*/ {  20.183*AU,  0.0*EE,  36.2*KB, 2.976*AA, 10.0*FS },
  /* 2 Ar*/ {  39.948*AU,  0.0*EE, 124.0*KB, 3.418*AA, 25.0*FS },
  /* 3 Kr*/ {  83.50 *AU,  0.0*EE, 190.0*KB, 3.610*AA, 25.0*FS },
  /* 4 Xe*/ { 131.30 *AU,  0.0*EE, 229.0*KB, 4.055*AA, 25.0*FS },
  /* 5 Hg*/ { 200.59 *AU,  0.0*EE, 851.0*KB, 2.898*AA, 25.0*FS },
  /* 6 H2*/ {   2.016*AU,  0.0*EE,  33.3*KB, 2.968*AA,  5.0*FS },
  /* 7 N2*/ {  28.013*AU,  0.0*EE,  91.5*KB, 3.681*AA, 20.0*FS },
  /* 8 O2*/ {  31.999*AU,  0.0*EE, 113.0*KB, 3.433*AA, 20.0*FS },
  /* 9 Ball*/{200.00 *AU,  0.0*EE, 124.0*KB, 50.0*AA, 25.0*FS }
  };
  int Nkind = 10;
  int GAS1 = 2;

  double mass[] = new double[Nkind]; /* k-th kind mass    */ 
  double epsi[] = new double[Nkind]; /* k-th kind epsilon */  
  double sigm[] = new double[Nkind]; /* k-th kind sigma   */  
  
  int kind[] = new int[NN];       /* i-th molec kind */
  double xx[] = new double[NN];   /* i-th x-position */
  double yy[] = new double[NN];   /* i-th y-position */
  double vx[] = new double[NN];   /* i-th x-velocity */
  double vy[] = new double[NN];   /* i-th y-velocity */
  double ffx[] = new double[NN];  /* i-th x-force    */
  double ffy[] = new double[NN];  /* i-th y-force    */
  Choice nChoice;
  Choice tChoice;
  Choice dtChoice;
  Choice mChoice;
  int Nmt=50+1;
/*----------------------------- applet control functions -----*/

  public void init() {
    resize(500,250);
    setBackground(Color.white);
/*---------GUI for changing number of molecules--by Ishii--*/
    nChoice = new Choice();
    nChoice.addItem("50");
    nChoice.addItem("1");
    nChoice.addItem("2");
    nChoice.addItem("3");
    nChoice.addItem("5");
    nChoice.addItem("7");
    nChoice.addItem("10");
    nChoice.addItem("20");
    nChoice.addItem("30");
    nChoice.addItem("40");
    nChoice.addItem("50");
    nChoice.addItem("100");
    nChoice.addItem("125");
    nChoice.addItem("150");
    nChoice.addItem("175");
    nChoice.addItem("200");
    add(nChoice);
    /* add(new Label("   Number of Molecule")); */
    nChoice.addItemListener(this);
/*---------GUI for changing number of molecules--by Ishii--*/

/*---------GUI for changing temperature--by Ishii--*/
    tChoice = new Choice();
    tChoice.addItem("300");
    tChoice.addItem("10");
    tChoice.addItem("50");
    tChoice.addItem("100");
    tChoice.addItem("200");
    tChoice.addItem("300");
    tChoice.addItem("400");
    tChoice.addItem("500");
    add(tChoice);
    /* add(new Label("Temperature")); */
    tChoice.addItemListener(this);
/*---------GUI for changing temperature of molecules--by Ishii--*/

/*---------GUI for changing time step--by Ishii--*/
    dtChoice = new Choice();
    dtChoice.addItem("25.0");
    dtChoice.addItem("10.0");
    dtChoice.addItem("5.0");
    add(dtChoice);
    dtChoice.addItemListener(this);
/*---------GUI for changing time step --by Ishii--*/

/*---------GUI for changing mass of -by Ishii--*/
    mChoice = new Choice();
    mChoice.addItem("200.0");
    mChoice.addItem("50.0");
    mChoice.addItem("100.0");
    mChoice.addItem("300.0");
    mChoice.addItem("500.0");
    mChoice.addItem("5000.0");
    mChoice.addItem("50000.0");
    mChoice.addItem("500000.0");
    add(mChoice);
    mChoice.addItemListener(this);
/*---------GUI for changing time step --by Ishii--*/

    dim = getSize();
    imgOff = createImage(1200,800);
    gOff = imgOff.getGraphics();

/*    bt_disp = new Button("Display on/off");
    bt_disp.addActionListener(this); */
    reset = new Button("Reset");
    reset.addActionListener(this); 

    scr= new Scrollbar(Scrollbar.HORIZONTAL,300,100,10,700);
    scr.addAdjustmentListener(this);

    setLayout(new BorderLayout());
    Panel pnl = new Panel();
    pnl.setLayout(new GridLayout(2,5));
/*    pnl.add(bt_disp); */
    pnl.add(nChoice);
    pnl.add(tChoice);
    pnl.add(mChoice);
    pnl.add(dtChoice); 
    pnl.add(reset);
    pnl.add(new Label("  Number of molecules"));
    pnl.add(new Label("Temperature[K]"));
    pnl.add(new Label("Mass[1.661x10^(-27)kg]"));
     pnl.add(new Label("        Timestep[fs]")); 
    /* pnl.add(scr); */
    /* pnl.add(new Label("  "));
    pnl.add(new Label("  "));*/
    add(pnl,"North");

    setInitialPosition();
  }

  public void itemStateChanged(ItemEvent e) {
  if(e.getSource() == nChoice) {
  String st=nChoice.getSelectedItem();
  Nmt=Integer.parseInt(st)+1;
  setInitialPosition();
    gOff.setColor(Color.white);
    gOff.fillRect(0,0,1200,800); 
  }
  if(e.getSource() == tChoice) {
  String st=tChoice.getSelectedItem();
  contTemp=Integer.parseInt(st);
  }
  if(e.getSource() == dtChoice) {
  String st=dtChoice.getSelectedItem();
  dt=Double.parseDouble(st)*1.0e-15;
  }
  if(e.getSource() == mChoice) {
  String st=mChoice.getSelectedItem();
  Molec[9][0]=Double.parseDouble(st)*AU;
  mass[9]=Double.parseDouble(st)*AU;
  }
  }




  public void start() {
    if (th == null) {
      th = new Thread(this);
      th.start();
    }
  }

  public void stop() {
    if (th != null) {
      th.stop();
      th = null;
    }
  }

  public void actionPerformed(ActionEvent ev){ 
  /*    if(ev.getSource() == bt_disp) {
       if(dispSW==1) {
         dispSW=0;
         gOff.setColor(Color.white);
         gOff.fillRect(0,0,1200,800); 
       } else if (dispSW==0) {
         dispSW=1;
       }
  } */ 

     if(ev.getSource() == reset) {
    setInitialPosition();
    gOff.setColor(Color.white);
    gOff.fillRect(0,0,1200,800); 
       }

  }

  public void adjustmentValueChanged(AdjustmentEvent ev){
    if(ev.getSource() == scr) { 
      contTemp = (double)(scr.getValue());
    }
  }

  public void run() {
    while (th != null) {
      try {
        calcposition();
        offPaint();
        repaint();
        Thread.sleep(sleepTime);
      } catch (InterruptedException e) { }
    }
  }


  public void update(Graphics g) {
    paint(g);
  }

  public void paint(Graphics g) {
    g.drawImage(imgOff,0,0,this);
  }

/* ----------------------------- offPaint -------------------- */

  void offPaint() {
    int i,ix,iy,ir,iiX,iiY;
    double tmp;

    if (dispSW==1) {
     gOff.setColor(Color.white);
    gOff.fillRect(0,0,1200,800); 

    tmp = temperature();
    gOff.setColor(Color.black); 
    if (tmp<contTemp) { gOff.setColor(Color.red); };
    gOff.drawRect(30,70,wakuX,wakuY);

      gOff.setColor(Color.cyan);
      for (i=0; i<Nmt-1; i++) {
        ix = (int)(paintSizeX*xx[i]/xMax)+30;
        iy = (int)(paintSizeY*yy[i]/yMax)+70;
        ir = (int)(3.0*sigm[kind[i]]/AA);
        gOff.fillOval(ix,iy, ir, ir);
      }

    gOff.setColor(Color.orange);
    i=Nmt-1;
    ix = (int)(paintSizeX*xx[i]/xMax)+30;
    iy = (int)(paintSizeY*yy[i]/yMax)+70;
    ir = (int)(3.0*sigm[kind[i]]/AA);
    gOff.fillOval(ix-ir/2,iy-ir/2, ir, ir);

    gOff.setColor(Color.black);
    gOff.drawString("cont T="+contTemp+"K",wakuX+50,70);
    gOff.setColor(Color.cyan);
    gOff.drawString("t="+Math.floor(t*1.0e12)+" ps",wakuX+50,90);
    gOff.drawString("T="+temperature()+" K",wakuX+50,110);
    }

    if (dispSW==0) {
    gOff.setColor(Color.black); 
    gOff.setColor(Color.black);
    gOff.drawString("cont T="+contTemp+"K",wakuX+50,70);
    gOff.drawRect(30,70,wakuX,wakuY);
    gOff.setColor(Color.orange);
    i=Nmt-1;
    ix = (int)(paintSizeX*xx[i]/xMax)+30;
    iy = (int)(paintSizeY*yy[i]/yMax)+70;
    ir = (int)(3.0*sigm[kind[i]]/AA);
    gOff.fillOval(ix,iy, ir/25, ir/25);
                   }
  }

/*-------------------------------------  statictics  ----------*/


  double rmsV() {
    int i;
    double s;

    s = 0.0; 
    for (i=0; i<Nmt; i++) {
      s += vx[i]*vx[i] + vy[i]*vy[i];
    }
    return ((double)Math.floor(Math.sqrt(s/(double)Nmt)*100.0)/100.0);
  }

  double temperature() {
    int i;
    double s;

    s = 0.0; 
    for (i=0; i<Nmt; i++) {
      s += 0.5*mass[kind[i]]*(vx[i]*vx[i] + vy[i]*vy[i]);
    }
    return ((double)Math.floor(s/((double)Nmt*1.38e-23)*10.0)/10.0 );
  }

/*-------------------------------  molecules motion   ---------*/
  void setInitialPosition(){
    int i,nn; 
    double s;

    for (i=0; i<Nkind; i++) {
      mass[i] = Molec[i][0]; 
      epsi[i] = Molec[i][2]; 
      sigm[i] = Molec[i][3];  
    };

    s = 3.4e-10;
    for (i=0; i<Nmt-1; i++) {
      kind[i] = GAS1;
      xx[i] = s*1.0*(double)(i%6)+2.5*s;
      yy[i] = s*1.0*(double)(i/6)+2.5*s;
      if(i>Nmt/4) {
      xx[i] = xMax-s*1.0*(double)(i%6)-2.5*s;
      yy[i] = s*1.0*(double)(i/6)+2.5*s;
                  }
      if(i>Nmt/2) {
      xx[i] = s*1.0*(double)(i%6)+2.5*s;
      yy[i] = yMax-s*1.0*(double)(i/6)-2.5*s;
                  }
      if(i>3*Nmt/4) {
      xx[i] = xMax-s*1.0*(double)(i%6)-2.5*s;
      yy[i] = yMax-s*1.0*(double)(i/6)-2.5*s;
                  }
      vx[i] = 80.0*Math.random()-40.0;
      vy[i] = 80.0*Math.random()-40.0;
      ffx[i] = 0.0;
      ffy[i] = 0.0;
        }
    i = Nmt-1;
    kind[i] = 9;
    /* xx[i] = xx[1]+6.0*s;
    yy[i] = yy[1]+6.0*s; */
    xx[i] = xMax/2;
    yy[i] = yMax/2;
    vx[i] = 0.0;
    vy[i] = 0.0;
    ffx[i] = 0.0;
    ffy[i] = 0.0; 

    /*  if (Nmt > 100) { 
        xx[i] = 0.75*xMax; yy[i] = 0.75*yMax;
      }; */
    
 }

  void calcposition(){
    int i;

    for (i=0; i< 100; i++) {
      timeEvolution();
    }
  }

  void timeEvolution(){
    int i; 
    double dtv2, rr, tmp;

    dtv2 = dt/2.0;
    t = t + dt;

    tmp = temperature();
    rr = Math.sqrt(contTemp/tmp);
    if(constMoment==1){rr=1;};
    if (rr<0.5) { rr = 0.5; } else if (rr>1.2){ rr=1.2; };

    for (i=0; i<Nmt; i++) {
      vx[i] += dtv2*ffx[i]/mass[kind[i]];
      vy[i] += dtv2*ffy[i]/mass[kind[i]];
      xx[i] += vx[i]*dt;
      yy[i] += vy[i]*dt;
    }
    forceCalc();
    for (i=0; i<Nmt; i++) {
      vx[i] += dtv2*ffx[i]/mass[kind[i]];
      vy[i] += dtv2*ffy[i]/mass[kind[i]];
    }

    for (i=0; i<Nmt-1; i++) {
      if (xx[i] < 0.0) { 
        xx[i] = 0.0; vx[i] = -rr*vx[i]; vy[i] = rr*vy[i]; 
      };
      if (xx[i] > xMax) {
        xx[i] = xMax; vx[i] = -rr*vx[i]; vy[i] = rr*vy[i]; 
      };
      if (yy[i] < 0.0) { 
        yy[i] = 0.0; vx[i] = rr*vx[i]; vy[i] = -rr*vy[i]; 
      };
      if (yy[i] > yMax) {
        yy[i] = yMax; vx[i] = rr*vx[i]; vy[i] = -rr*vy[i];
      };

    }
     /** change the boundary condition for Ball ** by Ishii **/
     i=Nmt-1;
      if (xx[i]-0.5*sigm[kind[i]]< 0.0) { 
        xx[i] =0.5*sigm[kind[i]]; vx[i] = -rr*vx[i]; vy[i] = rr*vy[i]; 
      };
      if (xx[i] > xMax-0.43*sigm[kind[i]]) {
        xx[i] =xMax-0.43*sigm[kind[i]]; vx[i] = -rr*vx[i]; vy[i] = rr*vy[i]; 
      };
      if (yy[i]-0.5*sigm[kind[i]]< 0.0) { 
        yy[i] =0.5*sigm[kind[i]] ; vx[i] = rr*vx[i]; vy[i] = -rr*vy[i]; 
      };
      if (yy[i] > yMax-0.43*sigm[kind[i]]) {
        yy[i] = yMax-0.43*sigm[kind[i]]; vx[i] = rr*vx[i]; vy[i] = -rr*vy[i];
    }
  }

  void forceCalc() {
    int i,j;
    double rij,f,fxij,fyij;

    for(i=0;i<Nmt;i++) {
      ffx[i] = 0.0;
      ffy[i] = 0.0;
    }

    for(i=0;i<Nmt;i++) {
      for(j=i+1;j<Nmt;j++) {
        rij = Math.sqrt((xx[i]-xx[j])*(xx[i]-xx[j])+(yy[i]-yy[j])*(yy[i]-yy[j]));
        f = force(rij,i,j);
        fxij = f*(xx[i]-xx[j])/rij;
        fyij = f*(yy[i]-yy[j])/rij;

        ffx[i] += fxij;
        ffy[i] += fyij;

        ffx[j] += -fxij;
        ffy[j] += -fyij;
      }
    }
  }

  double force(double r, int ii, int jj) {
    double s,ep,ri,r3,r6;

    s = 0.5*(sigm[kind[ii]]+sigm[kind[jj]]);
    ep = 0.5*(epsi[kind[ii]]+epsi[kind[jj]]);
    if(r>s+s/10000.000) {
    return 0.00;
    }
    else
    { 
    ri = s/(r);
    r3 = ri*ri*ri;
    r6 = r3*r3;
    return (24.0*ep*r6*(2.0*r6)/(r));
    }
  }

/*----- end of molecules motion -----*/

}  

/*----- end of applet -----*/
