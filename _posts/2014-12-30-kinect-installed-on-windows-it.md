---
title: Collegare il Kinect
excerpt: Note su provare il funzionamento del Kinect
date: 2014-12-30 23:35 +01:00
header:
  teaser: /assets/images/kinect/kinect.jpg
lang: it
ref: kinect
tags: elettronica

---

In quest'articolo descrivo come collegare il [Kinect](https://it.wikipedia.org/wiki/Microsoft_Kinect){:target="_blank"} a Linux tramite il software [Processing](https://processing.org/){:target="_blank"}.

Faccio riferimento all'ottimo tutorial di [Daniel Shiffman](http://shiffman.net/){:target="_blank"} presente nel suo [sito](http://shiffman.net/p5/kinect/){:target="_blank"}.

# Installazione

Come prima cosa, installare il software [Processing](https://processing.org/){:target="_blank"} scaricando il file compresso di installazione e decomprimendolo, come `sudo`, in `/opt` (io uso `sudo file-roller` e apro la GUI per decomprimere i file, aprendo il file appena scaricato e destinandolo in `/opt`. `file-roller` scrive in `/opt` solo se lanciato come `sudo`). Sempre come `sudo` navigare con il terminale nella cartella appena installata e lanciare il software via `sudo bash ./processing`.

Dopodichè installare la libreria **Open Kinect for Processing** `Sketch --> Import Library... --> Add Library...` anche se nella descrizione si menziona che è stata sviluppata per Mac OS.

# Accensione

Aprire poi l'esempio **RGBDepthTest** e lanciarlo.

![RGBDepthTest]({{ site.url }}/assets/images/kinect/rgbdepthtest.png)

![Immagine e profonditá in bianco e nero]({{ site.url }}/assets/images/kinect/image-and-bwdepth.png)

![Immagine in bianco e nero e profonditá a colori]({{ site.url }}/assets/images/kinect/bwimage-and-colordepth.png)

Le funzioni presenti nello sketch sono molto semplici.

Si importano le funzioni dalle librerie `openkinect` e `freenect`, e si inizializzano alcune variabili globali.

{% highlight java %}
// Daniel Shiffman
// All features test

// https://github.com/shiffman/OpenKinect-for-Processing
// http://shiffman.net/p5/kinect/

import org.openkinect.freenect.*;
import org.openkinect.processing.*;

Kinect kinect;

float deg;

boolean ir = false;
boolean colorDepth = false;
boolean mirror = false;
{% endhighlight %}

Nella funzione `setup`, eseguita solo una volta all'inizio del programma, si crea una finestra per l'output e si inizializzano il sensore di profonditá e la videocamera, e si salva l'inclinazione in una variabile.

{% highlight java %}
void setup() {
  size(1280, 520);
  kinect = new Kinect(this);
  kinect.initDepth();
  kinect.initVideo();
  kinect.enableColorDepth(colorDepth);
  deg = kinect.getTilt();
}
{% endhighlight %}

Nella funzione `draw`, continuamente eseguita in circolo, si visualizzano gli output del sensore di profonditá e della videocamera.

{% highlight java %}
void draw() {
  background(0);
  image(kinect.getVideoImage(), 0, 0);
  image(kinect.getDepthImage(), 640, 0);
  fill(255);
  text(
    "Press 'i' to enable/disable between video image and IR image,  " +
    "Press 'c' to enable/disable between color depth and gray scale depth,  " +
    "Press 'm' to enable/diable mirror mode, "+
    "UP and DOWN to tilt camera   " +
    "Framerate: " + int(frameRate), 10, 515);
}
{% endhighlight %}

Vi è inoltre la funzione `keyPressed` che sta in ascolto per i tasti premuti per alternare le due immagini tra bianco e nero e colore, e per la funzione "specchio" per girare l'immagine.

{% highlight java %}
void keyPressed() {
  if (key == 'i') {
    ir = !ir;
    kinect.enableIR(ir);
  } else if (key == 'c') {
    colorDepth = !colorDepth;
    kinect.enableColorDepth(colorDepth);
  }else if(key == 'm'){
    mirror = !mirror;
    kinect.enableMirror(mirror);
  } else if (key == CODED) {
    if (keyCode == UP) {
      deg++;
    } else if (keyCode == DOWN) {
      deg--;
    }
  deg = constrain(deg, 0, 30);
  kinect.setTilt(deg);
  }
}
{% endhighlight %}

# Informazioni di sistema

{% highlight shell %}
$ uname -mrs

# Linux 4.10.0-30-generic x86_64

$ lsb_release -a

# LSB Version:	core-9.20160110ubuntu5-amd64:core-9.20160110ubuntu5-noarch:security-9.20160110ubuntu5-amd64:security-9.20160110ubuntu5-noarch
# Distributor ID:	Ubuntu
# Description:	Ubuntu 17.04
# Release:	17.04
# Codename:	zesty
{% endhighlight %}
