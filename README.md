# Connect_5_Multi_Version
# It a game written in Python 2.7. You have to connect 5 stones in a row to win.


from Tkinter import *
from tkMessageBox import *
from datetime import *
import time
from tkSimpleDialog import askstring
import getpass


class double:
	def __init__(self):
		win = Tk()
		win.title('Connect Dots Multi ver. Dodo Bird')
		
		global Bin, Blackmove, Whitemove, Bluemove, Redmove, movecount, Turn, BoardSize, Bdcolor, PlayerName, P1N, P2N, P3N, P4N, CDchannel, CDChNum
		
		PSA = PlayerName+' joined game on channel '+ str(CDChNum)
		MonClerk(PSA)
		
		
		
		# Temp refresher
		C5Mfile = open(CDchannel,'w')
		C5Mfile.write('20000/20000/green\n')
		C5Mfile.close()
	
		Bin =[]
		if PlayerName == P1N:
			Youare = "You are Black"
			Topfg = 'white'
			Topbg = 'black'
		elif PlayerName == P2N:
			Youare = "You are White"
			Topfg = 'black'
			Topbg = 'white'
		elif PlayerName == P3N:
			Youare = "You are Blue"
			Topfg = 'white'
			Topbg = 'blue'
		elif PlayerName == P4N:
			Youare = "You are Red"
			Topfg = 'white'
			Topbg = 'red'

		self.TopLabel = Label(win,text = Youare, fg = Topfg, bg = Topbg)
		self.TopLabel.pack()

		#Move Count
		Blackmove = Whitemove = Bluemove = Redmove = 0

		movecount = 'WAITING FOR PLAYERS'
		self.MoveLabel = Label(win, text = movecount, font = 'arial 12')
		self.MoveLabel.pack()

		#Turn Label
		Turn = "Start the game"
		self.TurnLabel = Label(win,text = Turn, font = 'arial 12')
		self.TurnLabel.pack()
		
		#referee : Renaming Turn
		
		
		#Board
		if BoardSize is 'X':
			side = 900
		elif BoardSize is 'L':
			side = 700
		elif BoardSize is 'M':
			side = 550
		elif BoardSize is 'S':
			side = 400
			
		self.canv = Canvas(win,width = side, height = side, bg = Bdcolor, cursor = 'tcross')
		self.canv.pack()
		LINES = range(10,side,25)
		for LL in LINES:
			self.canv.create_line(10,LL,side-15,LL)
			self.canv.create_line(LL,10,LL,side-15)
		
		GameSUBt = Button(win,text = 'Game Start/Update', width = 15, command = self.extract)
		GameSUBt.pack()
			
		#Popup Menu
		self.menu = Menu(win,tearoff = 0)
		self.menu.add_command(label = 'Clear All',command = self.clear)
		self.menu.add_command(label = 'Chat', command = self.chat)
		self.menu.add_command(label = 'Mercy', command = self.mercy)
		
		win.bind('<Escape>',lambda x: win.destroy())
		self.KeyBinding()
		time.sleep(1)
		self.extract()

		win.mainloop()
	
	
	def KeyBinding(self):
	
		if HorG is 'H':
			self.canv.bind('<Button-1>',self.markinput_Black)
		elif HorG is 'G':
			self.canv.bind('<Button-1>',self.markinput_White)
			
		self.canv.bind('<Button-3>',self.popup)

	def alart(self):
		print 'warning',showwarning("Referee",'WAIT FOR YOUR TURN')

	def rightkey(self,event):
		print 'error',showerror('Referee','INVALID KEY PRESSED')

	def popup(self,event):
		self.menu.post(event.x_root, event.y_root)
		
	def chat(self):
		C5_single_chat()
		# chatwin = Tk()
		# self.chatcanv = Canvas(chatwin, width = 100, height = 100, bg='yellow')
		# self.chatcanv.pack()

	def clear(self):
		self.canv.delete('mark')	
		global Bin, Blackmove, Whitemove, Bluemove, Redmove, Turn
		Blackmove = Whitemove = Bluemove = Redmove = 0
		Turn = "RESTART THE GAME"
		C5Mfile = open(CDchannel,'w')
		C5Mfile.write('20000/20000/green\n')
		C5Mfile.close()
		self.TurnLabel['text'] = Turn
		self.TurnLabel['bg'], self.TurnLabel['fg'] = 'black','gold'
		Bin =[]
		Turn = ['Black','White','Blue','Red']
		print ('game reseted')		


	def clerk(self, move):
		moveN = str(move)+'\n'
		C5Sfile = open(CDchannel,'a')
		C5Sfile.write(moveN)
		C5Sfile.close()
		self.extract()

		
	def extract(self):
		global Bin
		while True:
			try:
				C5Sfile = open(CDchannel,'r')
				ff = C5Sfile.readlines()
				C5Sfile.close()
				Bin = []
				for Marks in ff:
					Bin.append(Marks[0:len(Marks)-1])
				self.display()
				time.sleep(.5)
				
				self.canv.update()
			except:
				time.sleep(.45)
				continue
	
	def mercy(self):
		global Bin, PlayerName
		Bin = Bin[0:(len(Bin)-1)]
		C5Mfile = open(CDchannel,'w')
		for ReBin in Bin:
			ReBin = ReBin+'\n'
			C5Mfile.write(ReBin)
		C5Mfile.close()
		MonClerk( PlayerName + ' begged for Mercy')
		#MonitorExtract()
		
	def compiler(self,X,Y,Color):
		XcooADJ = int(25*round(float(X-10)/25)+10)
		YcooADJ = int(25*round(float(Y-10)/25)+10)
		
		Mark = str(XcooADJ) + "/" + str(YcooADJ) + "/" + Color
		self.clerk(Mark)
		
	def display(self):
		
		global Bin, Blackmove, Whitemove, Bluemove, Redmove, Turn, movecount, player
		Blackmove = Whitemove = Bluemove = Redmove = 0
		self.canv.delete('mark')
		for ee in Bin:
			bb = ee.split('/')
			Xcoo, Ycoo, color2 = int(bb[0]), int(bb[1]), bb[2]
			x1, x2, y1, y2 = (Xcoo-8), (Xcoo+8), (Ycoo-8), (Ycoo+8)
			
			if color2 == 'black':
				Blackmove += 1
				Turn = "White"
				player = P2N
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'white', 'black'
			
			elif color2 == 'white':
				Whitemove += 1
				Turn = "Black"
				player = P1N
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'black','white'
			else:
				player = 'Start the Game'
				Turn = "Anyone"
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'black','gold'
			
			
				
				
			self.canv.create_oval(x1,y1,x2,y2, fill = color2, tags = 'mark')

		print Bin
			
		self.TurnLabel['text'] = ('{},{}\'s turn'.format(player,Turn))
		self.MoveLabel['text'] =('Black Move:{}   White Move:{}'.format(Blackmove,Whitemove))
			
			

	def markinput_Black(self,event):
		global Turn, Blackmove
		if 'Black' in Turn or 'Anyone'in Turn:
			color = 'black'
			X, Y, C = event.x, event.y, color
			self.compiler(X,Y,C)
		else: self.alart()
			
	def markinput_White(self,event):
		global  Turn, whitemove
		if 'White' in Turn or 'Anyone'in Turn:
			color = 'white'
			X, Y, C = event.x, event.y, color
			self.compiler(X,Y,C)
		else: self.alart()
##############################################################################

class triple(double):
	
	
	def KeyBinding(self):
		global PlayerName, P1N, P2N, P3N
		if PlayerName is P1N:
			self.canv.bind('<Button-1>',self.markinput_Black)
		elif PlayerName is P2N:
			self.canv.bind('<Button-1>',self.markinput_White)
		elif PlayerName is P3N:
			self.canv.bind('<Button-1>',self.markinput_Blue)
			
		self.canv.bind('<Button-3>',self.popup)	
	
	def markinput_Blue(self,event):
		global Turn, Bluemove
		if 'Blue' in Turn or 'Anyone'in Turn:
			color = 'blue'
			X, Y, C = event.x, event.y, color
			self.compiler(X,Y,C)
		else:
			self.alart()
			
	def display(self):
		
		global Bin, Blackmove, Whitemove, Bluemove, Redmove, Turn, movecount, player
		Blackmove = Whitemove = Bluemove = Redmove = 0
		self.canv.delete('mark')
		for ee in Bin:
			bb = ee.split('/')
			Xcoo, Ycoo, color2 = int(bb[0]), int(bb[1]), bb[2]
			x1, x2, y1, y2 = (Xcoo-8), (Xcoo+8), (Ycoo-8), (Ycoo+8)
			
			if color2 == 'black':
				Blackmove += 1
				Turn = "White"
				player = P2N
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'white', 'black'
			
			elif color2 == 'white':
				Whitemove += 1
				Turn = "Blue"
				player = P3N
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'blue','white'
			
			elif color2 == 'blue':
				Bluemove += 1
				Turn = "Black"
				player = P1N
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'black','white'			
				
			else:
				player = 'Start the Game'
				Turn = "Anyone"
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'black','gold'

				
			self.canv.create_oval(x1,y1,x2,y2, fill = color2, tags = 'mark')
			

		print Bin
			
		self.TurnLabel['text'] = ('{},{}\'s turn'.format(player,Turn))
		self.MoveLabel['text'] =('Black Move:{}   White Move:{}   Blue Move:{}'.format(Blackmove,Whitemove,Bluemove))		
			
		
			
	
###################################################################################		
class quad(triple):
	
	def KeyBinding(self):
		global PlayerName, P1N, P2N, P3N, P4N
		if PlayerName is P1N:
			self.canv.bind('<Button-1>',self.markinput_Black)
		elif PlayerName is P2N:
			self.canv.bind('<Button-1>',self.markinput_White)
		elif PlayerName is P3N:
			self.canv.bind('<Button-1>',self.markinput_Blue)
		elif PlayerName is P4N:
			self.canv.bind('<Button-1>',self.markinput_Red)
		
		self.canv.bind('<Button-3>',self.popup)	
	
			
	def markinput_Red(self,event):
		global Turn, Bluemove
		if 'Red' in Turn or 'Anyone'in Turn:
			color = 'red'
			X, Y, C = event.x, event.y, color
			self.compiler(X,Y,C)
		else:
			self.alart()
	

	def display(self):
		
		global Bin, Blackmove, Whitemove, Bluemove, Redmove, Turn, movecount, player
		Blackmove = Whitemove = Bluemove = Redmove = 0
		self.canv.delete('mark')
		for ee in Bin:
			bb = ee.split('/')
			Xcoo, Ycoo, color2 = int(bb[0]), int(bb[1]), bb[2]
			x1, x2, y1, y2 = (Xcoo-8), (Xcoo+8), (Ycoo-8), (Ycoo+8)
			
			if color2 == 'black':
				Blackmove += 1
				Turn = "White"
				player = P2N
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'white', 'black'
			
			elif color2 == 'white':
				Whitemove += 1
				Turn = "Blue"
				player = P3N
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'blue','white'
			
			elif color2 == 'blue':
				Bluemove += 1
				Turn = "Red"
				player = P4N
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'red','white'	

			elif color2 == 'red':
				Redmove += 1
				Turn = "Black"
				player = P1N
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'black','white'
				
			else:
				player = 'Start the Game'
				Turn = "Anyone"
				self.TurnLabel['bg'], self.TurnLabel['fg'] = 'black','gold'

				
			self.canv.create_oval(x1,y1,x2,y2, fill = color2, tags = 'mark')
			

		print Bin
			
		self.TurnLabel['text'] = ('{},{}\'s turn'.format(player,Turn))
		self.MoveLabel['text'] =('Black Move:{}   White Move:{}\nBlue Move:{}   Red Move:{}'.format(Blackmove,Whitemove,Bluemove,Redmove))		
			
	

###################################################################################



class C5_single_chat:pass
		
MonitorAdd = 'C:/Python27/C5_Multi_Monitor.txt'
# Preface
C5Monitor = open(MonitorAdd,'a')
C5Monitor.close()
######
MonBin = []
AA = 1
TT = 0

Bdcolor = 'grey'
BoardSize = 'S'
######

	
def PlayFor22():
	global HostGuest, HorG, PlayerName, AA
	if AA is 9: print 'warning',showwarning("Referee",'Already joined the game')
	else:
		PlayerName = name1.get()
		
		if PlayerName is '':
			ans = askyesno('Name', 'continue w/o name?')
			if ans is False:
				pass
			else:
				PlayerName = getpass.getuser()
				try:
					if HorG is 'H':HostBus2()	
					else:GuestBus2()
				except:print('Be sure to choose all the settings')
					
		else : 
			try:
				if HorG is 'H':HostBus2()	
				else:GuestBus2()
			except:print('Be sure to choose all the settings')

def PlayFor3():
	global HostGuest, HorG, PlayerName, AA
	if AA is 9: print 'warning',showwarning("Referee",'Already joined the game')
	else:
		PlayerName = name1.get()
		
		if PlayerName is '':
			ans = askyesno('Name', 'continue w/o name?')
			if ans is False:
				pass
			else:
				PlayerName = getpass.getuser()
				try:
					if HorG is 'H':HostBus3()	
					else:GuestBus3()
				except:
					print('Be sure to choose all the settings')
					MonClerk(' Choose all the settings')
					MonitorExtract()
		else : 
			try:
				if HorG is 'H':HostBus3()	
				else:GuestBus3()
			except:
				print('Be sure to choose all the settings')
				MonClerk(' Choose all the settings')
				MonitorExtract()
			
def PlayFor4():
	global HostGuest, HorG, PlayerName, AA
	if AA is 9: print 'warning',showwarning("Referee",'Already joined the game')
	else:
		PlayerName = name1.get()
		BoardSize = 'S'
		
		if PlayerName is '':
			ans = askyesno('Name', 'continue w/o name?')
			if ans is False:
				pass
			else:
				PlayerName = getpass.getuser()
				#try:
				if HorG is 'H':
					HostBus4()	
				else:
					GuestBus4()
				#except:
					# print('Be sure to choose all the settings')
					# MonClerk(' Choose all the settings')
					# MonitorExtract()
		else : 
			#try:
			if HorG is 'H':
				HostBus4()	
			else:
				GuestBus4()
			#except:
				# print('Be sure to choose all the settings')
				# MonClerk(' Choose all the settings')
				# MonitorExtract()

def MonClerk(Line):
	MonLine = datetime.now().strftime('%H:%M:%S ')+Line+'\n'
	C5Monitor = open(MonitorAdd,'a')
	C5Monitor.write(MonLine)
	C5Monitor.close()	
	
def MonCutter(MonLine):
	global MonBin
	MonLine = MonLine[len(MonLine)-6:]
	C5Monitor = open(MonitorAdd,'w')
	for ReMon in MonLine:
		C5Monitor.write(ReMon)
	C5Monitor.close()
	MonBin = []
	
def PreAbort():
	global AA, A, BoardSize, Bdcolor, PlayerName, P1N, P2N, P3N, P4N, CDchannel, CDChNum 
	if AA is 9:
		AA = 1
		A = 5
		Busfile = open(CDchannel,'w')
		Busfile.write('GameAbort*!*!*!*!')
		Busfile.close()
		BusLine = PlayerName + ' has Aborted the game <Channel ' +str(CDChNum) + ' >' 
		MonClerk(BusLine)
		MonitorExtract()
		PlayerName = P1N = P2N = P3N = P4N = ''
	else: print 'warning',showwarning("Referee","You haven't joined any game yet")
	
	
def PreHelp():
	helpwin = Tk()
	helpTXT = Text(helpwin, width = 40, height = 20, bg='grey', font = 'arial 8')
	helpTXT.pack()
	helpmsg = '''Hi, this is Connect Dots Multi PC version.'''
	helpTXT.insert(END,helpmsg)
	
def MonitorExtract():
	global AA, TT, MonBin
	if AA is 1:
		P = 0
		while AA is 1:
			if P > 5:
				P = 0 
				C5Monitor = open(MonitorAdd,'r')
				MonLine = C5Monitor.readlines()
				C5Monitor.close()
				if TT > 4:
					TT = 0
					if len(MonLine)>12:
						MonCutter(MonLine)
					else:pass
				else:
					TT += 1
					for MonL in MonLine:
						if MonL in MonBin:
							continue
						else:
							MonBin.append(MonL)
							MonBin.reverse()
							MonitorText.delete('1.0',END)
							for MonLL in MonBin:
								MonitorText.insert(END,MonLL)
							MonBin.reverse()
				#MonitorText.update()
			else: P += 1
			MonitorText.update()
			time.sleep(.2)
			
	elif AA is 9:
		C5Monitor = open(MonitorAdd,'r')
		MonLine = C5Monitor.readlines()
		C5Monitor.close()
		if TT > 4:
			TT = 0
			if len(MonLine)>12:
				MonCutter(MonLine)
			else:pass
		else:
			TT += 1
			for MonL in MonLine:
				if MonL in MonBin:
					continue
				else:
					MonBin.append(MonL)
					MonBin.reverse()
					MonitorText.delete('1.0',END)
					for MonLL in MonBin:
						MonitorText.insert(END,MonLL)
					MonBin.reverse()
		MonitorText.update()


def HostBus2():
	global P1N, P2N, Bdcolor, BoardSize, AA, PlayerName, CDChNum
	A = 0
	AA = 9
	P1N = PlayerName
	BusLine = 'player1/'+P1N+'/'+Bdcolor+'/'+BoardSize
	Busfile = open(CDchannel,'w')
	Busfile.write(BusLine)
	Busfile.close()
	PSA = PlayerName+' opened a game on channel '+ str(CDChNum)
	MonClerk(PSA)
	MonitorExtract()
	while A is 0:
		Busfile = open(CDchannel,'r')
		RR = Busfile.readline()
		try:
			RR = RR.split('*')
			BusCheck = RR[1].split('/')
			if BusCheck[0] == 'player2':
				P2N = BusCheck[1]
				A = 5
				AA = 5
				MonClerk('player 1 is green\nGame begins in 3 sec')
				MonitorExtract()
				time.sleep(2)
				print ('Player 1,2 are connected')
				Frontpage.destroy()
				double()
				sys.exit()
			else:
				print 'player is not 2'
				A = 5
				AA = 1
				MonitorExtract()
				
		except:
			MonClerk(' Host is waiting for player2')
			MonitorExtract()
			time.sleep(.5)
			

def GuestBus2():
	global P1N, P2N, Bdcolor, BoardSize, AA, PlayerName
	P2N = PlayerName
	A = 0
	AA = 9
	while A is 0:
		try:
			Busfile = open(CDchannel,'r')
			RR = Busfile.readline()
			Busfile.close()
			try:
				BusCheck = RR.split('/')
				if BusCheck[0] == 'player1':
					A = 5
					AA = 5
					BusLine = '*player2/'+PlayerName
					Busfile = open(CDchannel,'a')
					Busfile.write(BusLine)
					Busfile.close()
				
					P1N = BusCheck[1]
					Bdcolor = BusCheck[2]
					BoardSize = BusCheck[3]
					MonClerk('player 2 is green\nGame begins in 3 sec')
					MonitorExtract()
					print ('Player 1,2 are connected')
					time.sleep(2)
					Frontpage.destroy()
					double()
					sys.exit()
				else:
					print 'player1 is wrong'
					A = 5
					AA = 1
					MonitorExtract()
			except:
				print 'not slpiting'
				A = 5
				AA = 1
				MonitorExtract()
				
		except: 
			MonClerk('Host is not found')
			MonitorExtract()
			time.sleep(.5)
			

def HostBus3():
	global P1N, P2N, P3N, Bdcolor, BoardSize, AA, PlayerName, CDChNum
	A = 0
	AA = 9
	P1N = PlayerName
	
	BusLine = 'player1/'+P1N+'/'+Bdcolor+'/'+BoardSize
	Busfile = open(CDchannel,'w')
	Busfile.write(BusLine)
	Busfile.close()
	PSA = PlayerName+' opened a game on channel '+ str(CDChNum)
	MonClerk(PSA)
	MonitorExtract()
	while A is 0:
		Busfile = open(CDchannel,'r')
		RR = Busfile.readline()
		RR = RR.split('*')
		if len(RR) is 3:
			BusCheck = RR[1].split('/')
			if BusCheck[0] == 'player2':
				P2N = BusCheck[1]
				BusCheck = RR[2].split('/')
				if BusCheck[0] == 'player3':
					P3N = BusCheck[1]
					A = 5
					AA = 5
					MonClerk('Player 1,2,3 are green') 
					MonClerk('Game Starts in 4 sec')
					MonitorExtract()
					time.sleep(3.2)
					Frontpage.destroy()
					triple()
					sys.exit()
					
				else:
					A = 5
					AA = 5
					print 'Host cannot find player3'
			else: 
				A = 5
				AA = 5
				print 'Host cannot find player2'

		elif len(RR) is 5:
			A, AA = 5, 1
			print ('Game Aborted')
			
		else: 
			MonClerk(' Host is waiting for player 2,3')
			MonitorExtract()
			time.sleep(.5)
			
def GuestBus3():
	global P1N, P2N, P3N, Bdcolor, BoardSize, AA, PlayerName
	A = 0
	AA = 9
	while A is 0:
		try:
			Busfile = open(CDchannel,'r')
			RR = Busfile.readline()
			Busfile.close()
			BusCheck = RR.split('*')
			if len(BusCheck) == 1:
				BusCheck = BusCheck[0].split('/')
				if BusCheck[0] == 'player1':
					
					P2N = PlayerName
					BusLine = '*player2/'+ P2N
					Busfile = open(CDchannel,'a')
					Busfile.write(BusLine)
					Busfile.close()
					P1N = BusCheck[1]
					Bdcolor = BusCheck[2]
					BoardSize = BusCheck[3]
					MonClerk(' player2 is green')
					MonitorExtract()
					
					while A == 0:
						Busfile = open(CDchannel,'r')
						RR = Busfile.readline()
						Busfile.close()
						BusCheck = RR.split('*')
						if len(BusCheck) == 3:
							BusBoy = BusCheck[2].split('/')
							if BusBoy[0] == 'player3':
								P3N = BusBoy[1]
								MonClerk(' player2 is lunching')
								MonitorExtract()
								A = 5
								AA = 5
								time.sleep(3)
								Frontpage.destroy()
								triple()
								sys.exit()
							else: 
								A = 5
								AA = 1
								MonClerk(' player2 cannot meet player3')
								MonitorExtract()
						
						elif len(BusCheck) is 5:
							A, AA = 5, 1
							print ('Game Aborted')

						else:
							MonClerk(' player2 is waiting')
							MonitorExtract()
							time.sleep(.5)
							continue
						
						
				else:
					A = 5
					AA = 1
					MonClerk(' Host is not ready')
					MonitorExtract()
					
			
			elif len(BusCheck) == 2:
				BusBoy = BusCheck[0].split('/')
				if BusBoy[0] == 'player1':
					P1N = BusBoy[1]
					Bdcolor = BusBoy[2]
					BoardSize = BusBoy[3]
					BusBoy = BusCheck[1].split('/')
					if BusBoy[0] == 'player2':
						P2N = BusBoy[1]
						MonClerk(' Player3 is green')
						MonClerk(' Player3 is lunching')
						MonitorExtract()
						P3N = PlayerName
						BusLine = '*player3/'+ P3N
						Busfile = open(CDchannel,'a')
						Busfile.write(BusLine)
						Busfile.close()
						A = 5
						AA = 5
						time.sleep(3)
						Frontpage.destroy()
						triple()
						sys.exit()
					else:
						A = 5
						AA = 1
						MonClerk(' player3 cannot meet player2')
						MonitorExtract()	
				else:
					A = 5
					AA = 1
					MonClerk(' Player3 cannot meet the Host')
					MonitorExtract()
					
			elif len(BusCheck) is 5:
				A, AA = 5, 1
				print ('Game Aborted')
			
			else: 
				A = 5
				AA = 1
				MonClerk(' Wait for your game Host')
				MonitorExtract()				
		except:
			MonClerk(' Host not found')
			MonitorExtract()
			time.sleep(.5)
			
def HostBus4():
	global P1N, P2N, P3N, P4N, Bdcolor, BoardSize, AA, PlayerName, CDChNum
	A = 0
	AA = 9
	P1N = PlayerName
	print P1N, Bdcolor, BoardSize
	print '################'
	BusLine = 'player1/'+P1N+'/'+Bdcolor+'/'+BoardSize
	Busfile = open(CDchannel,'w')
	Busfile.write(BusLine)
	Busfile.close()
	PSA = PlayerName+' opened a game on channel '+ str(CDChNum)
	MonClerk(PSA)
	MonitorExtract()
	while A is 0:
		Busfile = open(CDchannel,'r')
		RR = Busfile.readline()
		RR = RR.split('*')
		if len(RR) is 4:
			BusCheck = RR[1].split('/')
			if BusCheck[0] == 'player2':
				P2N = BusCheck[1]
				BusCheck = RR[2].split('/')
				if BusCheck[0] == 'player3':
					P3N = BusCheck[1]
					BusCheck = RR[3].split('/')
					if BusCheck[0] == 'player4':
						P4N = BusCheck[1]
						A = 5
						AA = 5
						MonClerk(' Player 1,2,3,4 are green') 
						MonClerk(' Game Starts in 5 sec')
						MonitorExtract()
						time.sleep(4)
						Frontpage.destroy()
						quad()
						sys.exit()
					else: 
						A = 5
						AA = 1
						print 'Host cannot find player4'
				else: 
					A = 5
					AA = 1
					print 'Host cannot find player3'
			else: 
				A = 5
				AA = 1
				print 'Host cannot find player2'	
		
		elif len(RR) is 5:
			A, AA = 5, 1
			print ('Game Aborted')
		
		else: 
			MonClerk(' Host is waiting for player 2,3,4')
			MonitorExtract()
			time.sleep(.5)		
			
def GuestBus4():
	global P1N, P2N, P3N, P4N, Bdcolor, BoardSize, AA, PlayerName
	A = 0
	AA = 9
	while A is 0:
		# try:
		Busfile = open(CDchannel,'r')
		RR = Busfile.readline()
		Busfile.close()
		BusCheck = RR.split('*')
		if len(BusCheck) == 1:
			BusCheck = BusCheck[0].split('/')
			if BusCheck[0] == 'player1':
				P2N = PlayerName
				BusLine = '*player2/'+ P2N
				Busfile = open(CDchannel,'a')
				Busfile.write(BusLine)
				Busfile.close()
				P1N = BusCheck[1]
				Bdcolor = BusCheck[2]
				BoardSize = BusCheck[3]
				MonClerk(' Player2 is green')
				MonitorExtract()
				
				while A == 0:
					Busfile = open(CDchannel,'r')
					RR = Busfile.readline()
					Busfile.close()
					BusCheck = RR.split('*')
					if len(BusCheck) == 4:
						BusBoy = BusCheck[2].split('/')
						if BusBoy[0] == 'player3':
							P3N = BusBoy[1]
							BusBoy = BusCheck[3].split('/')
							if BusBoy[0] == 'player4':
								P4N = BusBoy[1]
								MonClerk(' player2 is lunching')
								MonitorExtract()
								A = 5
								AA = 5
								time.sleep(3.9)
								Frontpage.destroy()
								quad()
								sys.exit()
							else:
								A = 5
								AA = 1
								MonClerk(' player2 cannot meet player4')
								MonitorExtract()
						else: 
							A = 5
							AA = 1
							MonClerk(' player2 cannot meet player3')
							MonitorExtract()
					
					elif len(BusCheck) is 5:
						A, AA = 5, 1
						print ('Game Aborted')
					
					else:
						MonClerk(' Player2 is waiting')
						MonitorExtract()
						time.sleep(.5)
						continue
			else:
				A = 5
				AA = 1
				MonClerk(' player2 cannot meet the host')
				MonitorExtract()
				
		
		elif len(BusCheck) == 2:
			BusBoy1 = BusCheck[0].split('/')
			if BusBoy1[0] == 'player1':
				BusBoy2 = BusCheck[1].split('/')
				if BusBoy2[0] == 'player2':
					P3N = PlayerName
					BusLine = '*player3/'+P3N
					Busfile = open(CDchannel,'a')
					Busfile.write(BusLine)
					Busfile.close()
					P1N = BusBoy1[1]
					Bdcolor = BusBoy1[2]
					BoardSize = BusBoy1[3]
					P2N = BusBoy2[1]
					MonClerk(' Player3 is green')
					MonitorExtract()
					
					while A == 0:
						Busfile = open(CDchannel,'r')
						RR = Busfile.readline()
						Busfile.close()
						BusCheck = RR.split('*')
						if len(BusCheck) == 4:
							BusBoy4 = BusCheck[3].split('/')
							if BusBoy4[0] == 'player4':
								P4N = BusBoy4[1]
								MonClerk(' Player3 is lunching')
								MonitorExtract()
								A = 5
								AA = 5
								time.sleep(3.8)
								Frontpage.destroy()
								quad()
								sys.exit()
							else:
								A = 5
								AA = 1
								MonClerk(' player3 cannot meet player4')
								MonitorExtract()
						
						elif len(BusCheck) is 5:
							A, AA = 5, 1
							print ('Game Aborted')
						
						else: 
							MonClerk(' Player3 is waiting')
							MonitorExtract()
							time.sleep(.5)
							continue		
				else:
					A = 5
					AA = 1
					MonClerk(' player3 cannot meet player2')
					MonitorExtract()
			else:
				A = 5
				AA = 1
				MonClerk(' player3 cannot meet the Host')
				MonitorExtract()				
		
		elif len(BusCheck) == 3:
			BusBoy1 = BusCheck[0].split('/')
			if BusBoy1[0] == 'player1':
				BusBoy2 = BusCheck[1].split('/')
				if BusBoy2[0] == 'player2':
					BusBoy3 = BusCheck[2].split('/')
					if BusBoy3[0] == 'player3':
						P4N = PlayerName
						BusLine = '*player4/'+P4N
						Busfile = open(CDchannel,'a')
						Busfile.write(BusLine)
						Busfile.close()
						P1N = BusBoy1[1]
						Bdcolor = BusBoy1[2]
						BoardSize = BusBoy1[3]
						P2N = BusBoy2[1]
						P3N = BusBoy3[1]
						MonClerk(' Player4 is green')
						MonClerk(' Player4 is lunching')
						MonitorExtract()
						A = 5
						AA = 5
						time.sleep(3.7)
						Frontpage.destroy()
						quad()
						sys.exit()
					else:
						A = 5
						AA = 1
						MonClerk(' player4 cannot meet player3')
						MonitorExtract()	
					
				else:
					A = 5
					AA = 1
					MonClerk(' player4 cannot meet player2')
					MonitorExtract()	
			else:
				A = 5
				AA = 1
				MonClerk(' Player3 cannot meet the Host')
				MonitorExtract()
				
		elif len(BusCheck) is 5:
			A, AA = 5, 1
			print ('Game Aborted')
			
		else: 
			A = 5
			AA = 1
			MonClerk(' Wait for your game Host')
			MonitorExtract()				
		# except:
			# MonClerk(' Host not found')
			# MonitorExtract()
			# time.sleep(2)			
		
			
def BoardRadio():
	global BoardSize
	if Bsize.get() is 'X':
		BoardSize = 'X'
	elif Bsize.get() is 'L':
		BoardSize = 'L'
	elif Bsize.get() is 'M':
		BoardSize = 'M'
	elif Bsize.get() is 'S':
		BoardSize = 'S'

		
def BoardColorRadio():
	global Bdcolor
	if Bcolor.get() is 'B':
		Bdcolor = '#aa6611'
	elif Bcolor.get() is 'G':
		Bdcolor = 'grey'
		
def HorGRadio():
	global HostGuest, HorG
	if HostGuest.get() is 'H':
		HorG = 'H'
	elif HostGuest.get() is 'G':
		HorG = 'G'

def PreChannel():
	global PreRadio, CDchannel1, CDchannel2, CDchannel3, CDchannel, CDChNum

	if PreRadio.get() == '1':
		CDchannel = 'C:/Python27/C5_Multi_Move1.txt'
		CDChNum = 1
	elif PreRadio.get() == '2':
		CDchannel = 'C:/Python27/C5_Multi_Move2.txt'
		CDChNum = 2
	elif PreRadio.get() == '3':
		CDchannel = 'C:/Python27/C5_Multi_Move3.txt'
		CDChNum = 3		

		

opentime = datetime.strptime('2018-1-3', '%Y-%m-%d')
nowtime = datetime.now()
if opentime < nowtime: Frontpage = Tk()
Frontpage.title('Connect dots preface. DodoBird')

fr1 = Frame(Frontpage)
fr2 = Frame(Frontpage)
fr3 = Frame(Frontpage)
fr4 = Frame(Frontpage)
fr5 = Frame(Frontpage)
fr6 = Frame(Frontpage)
fr7 = Frame(Frontpage)
fr8 = Frame(Frontpage)
fr9 = Frame(Frontpage)
fr10 = Frame(Frontpage)
fr11 = Frame(Frontpage)
fr12 = Frame(Frontpage)
fr13 = Frame(Frontpage)
fr14 = Frame(Frontpage)
fr15 = Frame(Frontpage)

Title_Label = Label(fr9, text = 'Connect Dots	\n	Multi PC ver.', width = 18, font = 'aharoni 12')
Title_Label.pack()

FrontLabel = Label(fr1, text = 'How many players?', width = 25)
NameLabel = Label(fr4, text = 'Your name please\nex) Dodo.B')
PlayerNumButton2 = Button(fr2, text = 'Two', command = PlayFor22)
PlayerNumButton3 = Button(fr2, text = 'Three', command = PlayFor3)
PlayerNumButton4 = Button(fr2, text = 'Four', command = PlayFor4)

name1 = StringVar()
Name1ent = Entry(fr3,textvariable = name1, width = 15, bg = 'grey', font = 'arial 10')
Name1Lab = Label(fr3,text = 'Name', bg = 'black', fg = 'white', font = 'arial 10')

FrontLabel.pack()
PlayerNumButton2.grid(row =0, column = 0)
PlayerNumButton3.grid(row =0, column = 1)
PlayerNumButton4.grid(row =0, column = 2)

Name1ent.grid(row =0, column = 1)
Name1Lab.grid(row =0, column = 0)
NameLabel.pack()

Bsize = StringVar()
BoardSizeLb = Label(fr5, text = 'Board Size (Host Only)', width = 25, font = 'arial 10')
BS_XL = Radiobutton(fr6, text = 'XL', variable = Bsize, value = 'X' ,command = BoardRadio)
BS_L = Radiobutton(fr6, text = 'LG', variable = Bsize, value = 'L' ,command = BoardRadio)
BS_M = Radiobutton(fr6, text = 'MD', variable = Bsize, value = 'M' ,command = BoardRadio)
BS_S = Radiobutton(fr6, text = 'SM', variable = Bsize, value = 'S' ,command = BoardRadio)

BoardSizeLb.grid(column = 0, row = 0)
BS_XL.grid(column = 3, row = 0)
BS_L.grid(column = 2, row = 0)
BS_M.grid(column = 1, row = 0)
BS_S.grid(column = 0, row = 0)

Bcolor = StringVar()
BoardColor = Label(fr7, text = 'Board Type (Host Only)', width = 20, font = 'arial 10')
BC_B = Radiobutton(fr8, text = 'Wood', variable = Bcolor, value = 'B' ,command = BoardColorRadio)
BC_G = Radiobutton(fr8, text = 'Stone', variable = Bcolor, value = 'G' ,command = BoardColorRadio)
BoardColor.pack()
BC_B.grid(column = 0, row = 0)
BC_G.grid(column = 1, row = 0)

HostGuest = StringVar()
HostGuestLB = Label(fr10, text = 'You are', width = 20, font = 'arial 10')
HG_H = Radiobutton(fr11, text = 'Host', variable = HostGuest, value = 'H' ,command = HorGRadio)
HG_G = Radiobutton(fr11, text = 'Guest', variable = HostGuest, value = 'G' ,command = HorGRadio)
HostGuestLB.pack()
HG_H.grid(column = 0, row = 0)
HG_G.grid(column = 1, row = 0)

PreRadio = StringVar()
CDChannelRadio = Label(fr13, text = 'Joining Channel', width = 20, font = 'arial 10')
PrerdB1 = Radiobutton(fr14, variable = PreRadio, value = '1', command = PreChannel, text = '1')
PrerdB2 = Radiobutton(fr14, variable = PreRadio, value = '2', command = PreChannel, text = '2')
PrerdB3 = Radiobutton(fr14, variable = PreRadio, value = '3', command = PreChannel, text = '3')
CDChannelRadio.pack()
PrerdB1.grid(row =0, column =0)
PrerdB2.grid(row =0, column =1)
PrerdB3.grid(row =0, column =2)

MonitorText = Text(fr12, width = 45, bg = 'light grey', font = 'arial 10' )
MonitorText.pack()

AbortBt = Button(fr15, text = 'Abort the Game', font = 'arial 10', command = PreAbort)
HelpBt = Button(fr15, text = 'Help', font = 'arial 10', command = PreHelp)
AbortBt.grid(row = 0, column = 0)
HelpBt.grid(row = 0, column = 1)


fr9.grid(column = 0, row = 0)
fr4.grid(column = 0, row = 1)
fr3.grid(column = 0, row = 2)
fr5.grid(column = 0, row = 3)
fr6.grid(column = 0, row = 4)
fr7.grid(column = 0, row = 5)
fr8.grid(column = 0, row = 6)
fr10.grid(column = 0, row = 7)
fr11.grid(column = 0, row = 8)
fr13.grid(column = 0, row = 9)
fr14.grid(column = 0, row = 10)
fr1.grid(column = 0, row = 11)
fr2.grid(column = 0, row = 12)
fr12.grid(column = 1, row = 0, rowspan = 13)
fr15.grid(column = 0, row = 13, columnspan = 2)

Frontpage.bind('<Escape>',lambda x: Frontpage.destroy())


MonitorExtract()
