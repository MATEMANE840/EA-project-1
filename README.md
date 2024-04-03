  import tkinter as tk
from tkinter import ttk
import classesBroker as alpha
import time
import matplotlib
from matplotlib . backends . backend tkagg import FigureCanvasTkAgg
from matplotlib . figure import Figure
import matplotlib . pyplot as plt
import fxcmpy
import matplotlib . dates as mdates
import DBConnection as dbc
from hi s to ricCla s se s import Agent
from historicClasses import Price
import threading
m a t pl o tli b . u se (”TkAgg” )
LARGE FONT = ( ’ Verdana ’ , 20 )
currency = ’ ’ ,
start year = 0
end year = 0
token = ’ ’
threshold = 0.01
agents = []
historic text = ’’
historic title = ’’
cont = 0
data = None
li v e = True
con = None
def trade () : 
 global currency , agents , live
initiate traders ()
while live :
time . sleep (3)
try :
last p rice = con . ge t last price (currency )
for agent in agents :
agent . trade ( last price )
except :
time . sleep (10)
con . close ()
initiate traders ()
def initiate traders () :
global agents , con , currency
con = fxcmpy . fxcmpy ( access t o k e n=token , l o g le vel =’error ’ )
con . subscribe market data (currency )
agent long = alpha . Agent (con , currency , 0.001 , agent mode=’
long ’)
agent short = alpha . Agent (con , currency , 0.001 , agent mode=’
short ’)
agent long2 = alpha . Agent (con , currency , 0.0025 , agent mode=’
long ’)
agent short2 = alpha . Agent (con , currency , 0.0025 , agent mode=’
short ’)
agents = [ agent long , agent short , agent long2 , agent short2 ]
def run historic () :
global currency , start year , end year , threshold ,
historic text , historic ti tle , data
dataset = dbc . get dataset (currency , start year , end year )
dbc . reset database ()
time . sleep (1)
agent long = Agent ( o riginal t h r e s h ol d=threshold , agent mode=’
 long ’)
agent short = Agent ( o riginal t h r e s h ol d=threshold , agent mode=’
short ’)
fo r row in dataset :
id = row [ 0 ]
datetime = dbc . datetime t o floa t ( s t r ( row [ 2 ] ) , s t r ( row [ 3 ] ) )
bid = floa t ( row [ 5 ] )
ask = floa t ( row [ 6 ] )
price = Price (id , ask , bid , datetime )
agent long . trade ( price )
agent short . trade ( price )
dbc . g e t total stats ()
stats data = dbc . p rin t stats ()
data = dbc . ge t liquid value ()
time . sleep (1)
historic text = \
f ’ Opened orders : { stats d a t a . l o c [ 0 , ”OPENED ORDERS”]} \ n\n ’
\
f ’ Closed orders : { stats d a t a . l o c [ 0 , ”CLOSED ORDERS”]} \ n\n ’
\
f ’ Orders closed with profit : { stats data . loc [0 , ”
TAKE PROFIT ORDERS”]} \ n\n ’ \
f ’ Orders closed with stop loss : { stats data . loc [0 , ”
STOP LOSS ORDERS”]} \ n\n ’ \
f ’ Percentage of closed orders : { stats data . loc [0 , ”
CLOSED ORDERS PERCENTAGE”] } %\n\n ’ \
f ’ Percentage of orders closed with profit : { stats data . loc
[ 0 , ”TAKE PROFIT PERCENTAGE”] } %\n\n ’ \
f ’ Percentage of orders closed with stop loss : { stats data .
l o c [ 0 , ”STOP LOSS PERCENTAGE”] } %\n\n ’
historic title = f’ { currency } results between { start year } and
{ end year }\n\n ’
print (0)
c l a s s Main ( tk . Tk) :
def init ( self , ⇤ args , ⇤⇤ kwargs ) :
 tk . Tk . init ( self , ⇤ args , ⇤⇤ kwargs )
tk . Tk . wm title ( s elf , ’AlphaPy ’ )
con taine r = tk . Frame ( s e l f )
c o n t ai n e r . pack ( s i d e=”top ” , f i l l =’both ’ , expand=True )
container . grid rowconfigu re (0 , weight=1)
container . grid columnconfigu re (0 , weight=1)
self . frames = {}
for F in ( StartPage , LiveStart , HistoricStart , Live ,
Historic , GraphPage ) :
frame = F( container , s elf )
self . frames [F] = frame
frame . g ri d ( row=0, column=0, s t i c k y=”nsew ” )
self . show frame ( StartPage )
def show frame ( self , cont ) :
frame = self . frames [ cont ]
frame . tkraise ()
def stop live ( self , cont ) :
global con , li v e
con . close ()
live = False
frame = self . frames [ cont ]
frame . tkraise ()
def change t o live ( self , cont , token var , currency var ):
global token , currency , live
li v e = True
token = token var . get ()
currency aux = currency var . curselection ()
currency = currency var . get (currency aux )
x = threading . Thread ( ta rge t=trade )
x. start ()
frame = self . frames [ cont ]
frame . tkraise ()
def change t o hi s to ric ( s elf , cont , start , end , threshold var ,
currency var ):
global threshold , start year , end year , currency
start year = int ( start . get () )
end year = int (end . get () )
threshold = float ( threshold var . get () )
currency aux = currency var . curselection ()
currency = currency var . get (currency aux )
run historic ()
frame = self . frames [ cont ]
frame . t i t l e . config ( text=h i s t o r i c title )
frame . text . config ( text=hi s t o ri c text )
frame . tkraise ()
def change to graph ( self , cont ) :
global data
liquid v a l u e = data [ ’BALANCE’ ] . t o l i s t ( )
da t e s = data [ ’DATE’ ] . t o l i s t ( )
frame = self . frames [ cont ]
frame .a. clear ()
frame .a. s e t title (f ’ Results of { currency } with threshold {
threshold }\n\n’)
frame .a. plot ( dates , liquid value )
frame . canvas . draw ()
frame . tkraise ()
cl a s s StartPage ( tk . Frame ) :
def init ( self , parent , controller ) :
tk . Frame . init ( self , parent )
l a b e l = tk . Label ( s e l f , t e x t =’Welcome ! ! ! ’ , f o n t=LARGE FONT,
 label . pack (pady=70, padx=250)
live b u t t o n = t t k . Button ( s e l f , t e x t=”Li ve Trading ” ,
command=lambda : c o n t r o l l e r . show frame ( LiveStart ) )
live bu t ton . pack ( pady=50)
historic b u t t o n = t t k . Button ( s e l f , t e x t=” H i s t o r i c Trading
” ,
command=lambda : c o n t r o l l e r .
show frame ( HistoricStart ) )
historic bu t ton . pack ( pady=50)
blank s pa c e = tk . Label ( s e l f , t e x t =””, f o n t=LARGE FONT,
width=”20”, height=”3”)
blank space . pack ( pady=20)
cl a s s LiveS ta r t ( tk . Frame ) :
global token
def init ( self , parent , controller ) :
tk . Frame . init ( self , parent )
blank s pa c e = tk . Label ( s e l f , t e x t =””, f o n t=LARGE FONT)
blank space . pack (pady=40, padx=400)
l a b e l = tk . Label ( s e l f , t e x t=” I n s e r t your token ” , f o n t =17)
la b el . pack ( pady=10, padx=10)
token var = tk . StringVar ()
token entry = ttk . Entry ( self , width=30, textvariable=
token var )
token entry . pack ( pady=10, padx=10)
our to k e n = tk . Label ( s e l f , t e x t=” I f you don ’ t have a token
, you can use ours : \n”
” 21 c5c79ea3fa48989a892a9e496a0bc7d4cf9480 ” , font =14)
our token . pack ( pady=5, padx=10)
blank s pa c e2 = tk . Label ( s e l f , t e x t =””, f o n t=LARGE FONT)
blank space2 . pack ( pady=20)l a b e l = tk . Label ( s e l f , t e x t=” S e l e c t your c u r r e n c y ” , f o n t
=17)
la b el . pack ( pady=10, padx=10)
c u r r e n c i e s = tk . S t ringVa r ( val u e =[ ’EUR/USD’ , ’GBP/USD’ , ’
USD/JPY’ , ’EUR/JPY’ , ’USD/EUR’ , ’BTC/USD’ ] )
currency entry = tk . Listbox ( s elf , li s t v a ri a bl e=currencies ,
height=6)
currency entry . pack ()
blank s pa c e3 = tk . Label ( s e l f , t e x t =””, f o n t=LARGE FONT)
blank space3 . pack ( pady=20)
start b u t t o n = t t k . Button ( s e l f , t e x t=”S t a r t ” , command=
lambda : co n t roll e r . change t o live (Live , token var ,
currency entry
)
)
start button . pack ( pady=10, padx=10)
back bu t ton = t t k . Button ( s e l f , t e x t=”BACK” , command=lambda
: controller . show frame ( StartPage ) )
back button . pack ( pady=10, padx=10)
cl a s s Hi s to ri c S ta r t ( tk . Frame ) :
def init ( self , parent , controller ) :
tk . Frame . init ( self , parent )
blank s pa c e0 = tk . Label ( s e l f , t e x t =””, f o n t=LARGE FONT)
blank space0 . pack ( pady=5)
l a b e l = tk . Label ( s e l f , t e x t=” S e l e c t s t a r t yea r ” , f o n t =17)
la b el . pack ( pady=5)
start d a t e = t t k . Combobox ( s e lf , val u e s = ( ’2001 ’ , ’2002 ’ ,
’2003’, ’2004’, ’2005’, ’2006’, ’2007’, ’2008’,
’2009’, ’2010’,
’2011’, ’2012’,
’2013’,
’2014’, ’2015’,
’2016’,
 ’2017’, ’2018’,
’2019’) )
start date . pack ()
l a b e l = tk . Label ( s e l f , t e x t=” S e l e c t end yea r ” , f o n t =17)
la b el . pack ( pady=10)
finish d a t e = t t k . Combobox ( s e lf , val u e s = ( ’2001 ’ , ’2002 ’ ,
’2003’, ’2004’, ’2005’, ’2006’, ’2007’, ’2008’,
’2009’, ’2010’,
’2011’, ’2012’,
’2013’,
’2014’, ’2015’,
’2016’,
’2017’, ’2018’,
’2019’) )
finish date . pack ()
blank s pa c e1 = tk . Label ( s e l f , t e x t =””, f o n t=LARGE FONT)
blank space1 . pack ( pady=5)
l a b e l = tk . Label ( s e l f , t e x t=” S e l e c t a t h r e s h ol d ” , f o n t =17)
la b el . pack ( pady=7, padx=10)
threshold e n t r y = t t k . Combobox ( s e lf , val u e s = ( ’0.01 ’ ,
’0.005 ’ , ’0.0025 ’, ’0.0015 ’) )
threshold entry . pack ()
blank s pa c e2 = tk . Label ( s e l f , t e x t =””, f o n t=LARGE FONT)
blank space2 . pack ( pady=5)
l a b e l = tk . Label ( s e l f , t e x t=” S e l e c t your c u r r e n c y ” , f o n t
=17)
la b el . pack ( pady=7, padx=10)
currencies = tk . StringVar ( value=[’eur usd ’ , ’gbp usd ’ , ’
usd cad ’ , ’ usd jpy ’ , ’eur gbp ’ , ’ eur jpy ’ , ’ eur chf ’])
currency entry = tk . Listbox ( s elf , li s t v a ri a bl e=currencies ,
height=7)
currency entry . pack ()
blank space3 = tk . Label ( s elf , text =””, font =17)
blank space3 . pack ( pady=5)
 wait l a b e l = tk . Label ( s e l f , t e x t=f ’ This p r o c e s s may ta ke a
while , \ n\n ’
f ’ Please be patient \n’,
font =17)
wait label . pack ()
start b u t t o n = t t k . Button ( s e l f , t e x t=”S t a r t ” , command=
lambda : co n t roll e r . change t o historic (Historic ,
start date , finish date , threshold entry ,
currency entry ))
start button . pack ( pady=10, padx=10)
back bu t ton = t t k . Button ( s e l f , t e x t=”BACK” , command=lambda
: controller . show frame ( StartPage ) )
back button . pack ( pady=10, padx=10)
blank space4 = tk . Label ( s elf , text =””, font =17)
blank space4 . pack ( pady=10)
cl a s s Live ( tk . Frame ) :
def init ( self , parent , controller ) :
tk . Frame . init ( self , parent )
blank s pa c e0 = tk . Label ( s e l f , t e x t =””, f o n t=LARGE FONT)
blank space0 . pack ( pady=20)
algo s t a t u s = tk . Label ( s e l f , t e x t=f ’ The algo ri t hm i s now
t r a di n g a u toma ti call y ’ , f o n t=LARGE FONT)
algo s ta tu s . pack ( pady=20)
monitor t e x t = tk . Label ( s e l f , t e x t=f ’ You can monitor your
o r d e r s a t h t t p s : / / t r a d i n g s t a t i o n . fxcm . com ’ , f o n t=
LARGE FONT)
monitor text . pack ( pady=20)
user i n f o = tk . Label ( s e l f , t e x t=f ’ I f you a re u sing our
token , use thi s user and password to access the website
: \n\n ’
f ’ User : D261146687\n ’
f ’ Password : 6651\n ’ , font=
LARGE FONT)
user i nfo . pack ( pady=20)
 blank s pa c e1 = tk . Label ( s e l f , t e x t =””, f o n t=LARGE FONT)
blank space1 . pack ( pady=20)
back bu t ton = t t k . Button ( s e l f , t e x t=”BACK” , command=lambda
: controller . stop live (StartPage ) )
back button . pack (padx=200)
cl a s s Hi s to ri c ( tk . Frame ) :
global start year , end year , historic text , currency ,
historic title
def init ( self , parent , controller ) :
tk . Frame . init ( self , parent )
blank s pa c e0 = tk . Label ( s e l f , t e x t =””, f o n t=LARGE FONT)
blank space0 . pack ( pady=10)
s e l f . t i t l e = tk . Label ( s elf , text=h i s t o r i c t i t l e , font=
LARGE FONT)
s e lf . t i t l e . pack ( pady=20, padx=10)
s e l f . text = tk . Label ( s elf , text=hi s to ri c t e x t , font =17)
s e lf . text . pack ( pady=10, padx=10)
graph bu t ton = t t k . Button ( s e l f , t e x t=”Graph of the l i q u i d
val u e ” , command=lambda : c o n t r o l l e r . c ha ng e to graph (
GraphPage ) )
graph button . pack ( pady=10, padx=10)
back bu t ton = t t k . Button ( s e l f , t e x t=”Home” , command=lambda
: controller . show frame ( StartPage ) )
back button . pack ( pady=10, padx=10)
cl a s s GraphPage ( tk . Frame ) :
global start year , end year , historic text , currency ,
historic title , threshold
def init ( self , parent , controller ) :
tk . Frame . init ( self , parent )