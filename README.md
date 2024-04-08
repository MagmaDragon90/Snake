public class SnakeGame 
{

	public static void main(String[] args)
	{
		new GameFrame();
	}
}


import javax.swing.JFrame;

public class GameFrame extends JFrame
{
	//Constructor, Initializes a new GameFrame
	GameFrame()
	{
		this.add(new GamePanel());
		this.setTitle("Snake");
		this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
		this.setResizable(false);
		this.pack();	//Added components to the JFrame will be added to the GameFrame
		this.setVisible(true);
		this.setLocationRelativeTo(null); //Sets location of GameFrame to the
										  //middle of the screen
	}
}


import java.awt.*;
import java.awt.event.*;
import javax.swing.*;
import java.util.Random;

public class GamePanel extends JPanel implements ActionListener
{
	//Variable initialization
	static final int SCREEN_WIDTH = 600; //Screen Width
	static final int SCREEN_HEIGHT = 600;//Screen Height
	static final int UNIT_SIZE = 25;	 //Dimension of the objects of the game
	static final int GAME_UNITS = (SCREEN_WIDTH*SCREEN_HEIGHT)/UNIT_SIZE; // Total Size
														// of the snake game window
	static final int DELAY = 75;		//Speed of the Snake
	final int x[] = new int[GAME_UNITS]; //X Coordinates of the Snake
	final int y[] = new int[GAME_UNITS]; //Y Coordinates of the Snake
	int bodyParts = 6; //Initial size of the Snake
	int applesEaten;   //Score of the game
	int appleX;		   //X Coordinates of the apple
	int appleY;		   //Y Coordinates of the apple	
	char direction = 'R'; //Initial direction of the Snake
	boolean running = false; //Defines if the program is running
	Timer timer;			 //Game Timer
	Random random;			 //RNG
	

	GamePanel()
	{
		random = new Random(); //New instance of Random class
		this.setPreferredSize(new Dimension(SCREEN_WIDTH,SCREEN_HEIGHT)); //Window Size
		this.setBackground(Color.black); //Sets background color
		this.setFocusable(true);
		this.addKeyListener(new MyKeyAdapter());
		startGame();
		
	}
	public void startGame()
	{
		newApple();						//Sets a new instance of apple object
		running = true;					//Sets snake movement
		timer = new Timer(DELAY,this);	//New instance of Game Timer
		timer.start();					//Start of the Game Timer
	}
	public void paintComponent(Graphics g)
	{
		super.paintComponent(g);
		draw(g);
	}
	public void draw(Graphics g)
	{
		if(running)
		{
			//Grid for Snake
			for(int i=0;i<SCREEN_HEIGHT/UNIT_SIZE;i++)
			{
				g.drawLine(i*UNIT_SIZE,0,i*UNIT_SIZE,SCREEN_HEIGHT);
				g.drawLine(0,i*UNIT_SIZE,SCREEN_WIDTH,i*UNIT_SIZE);
			}
			//Draws a new apple object at random coordinates
			g.setColor(Color.red);
			g.fillOval(appleX, appleY, UNIT_SIZE, UNIT_SIZE);
			//Draws new Snake object
			for(int i = 0;i<bodyParts;i++)
			{
				//Head of the Snake
				if(i==0)
				{
					g.setColor(Color.green);
					g.fillRect(x[i], y[i], UNIT_SIZE, UNIT_SIZE);
				}
				//Body of the Snake
				else
				{
					g.setColor(new Color(137, 0, 255));
					g.fillRect(x[i], y[i], UNIT_SIZE, UNIT_SIZE);
				}
			}
			g.setColor(Color.red);
			g.setFont(new Font("Ink Free",Font.BOLD,40));
			FontMetrics metrics = getFontMetrics(g.getFont());
			g.drawString("Score: " + applesEaten, (SCREEN_WIDTH - metrics.stringWidth("Score: " + applesEaten))/2, g.getFont().getSize());
			
		}
		else
			gameOver(g);
	}
	//Generation of random coordinates for the apple object
	public void newApple()
	{
		//Sets random coordinates for the new apple object
		//Ensures that the new coordinate is an integer for game stability
		appleX = random.nextInt((int)(SCREEN_WIDTH/UNIT_SIZE))*UNIT_SIZE;
		appleY = random.nextInt((int)(SCREEN_HEIGHT/UNIT_SIZE))*UNIT_SIZE;
	}
	public void move()
	{
		//Shifts the bodyparts of the snake for the appearance of movement
		for(int i = bodyParts;i>0;i--)
		{
			x[i] = x[i-1];
			y[i] = y[i-1];
		}
		switch(direction)
		{
		case 'U':
			y[0] = y[0] - UNIT_SIZE;
			break;
		case 'D':
			y[0] = y[0] + UNIT_SIZE;
			break;
		case 'L':
			x[0] = x[0] - UNIT_SIZE;
			break;
		case 'R':
			x[0] = x[0] + UNIT_SIZE;
			break;
		
		}
		
	}
	public void checkApple()
	{
		if((x[0] == appleX) && (y[0] == appleY))
		{
			bodyParts++;
			applesEaten++;
			newApple();
		}
	}
	public void checkCollisions()
	{
		//Checks collision with Snake body
		for(int i = bodyParts;i>0;i--)
		{
			if((x[0] == x[i])&&(y[0]==y[i]))
			{
				running = false;
			}
		}
		//Checks Border collision
		//Check collision with Left border
		if(x[0] < 0)
			running = false;
		//Check collision with Right border
		if(x[0] > SCREEN_WIDTH)
			running = false;
		//Check collision with Top border
		if(y[0] < 0)
			running = false;
		//Check collision with Bottom border
		if(y[0] > SCREEN_HEIGHT)
			running = false;
		//Stops Game Timer
		if(!running)
			timer.stop();
		
	}
	public void gameOver(Graphics g)
	{
		//Score
		g.setColor(Color.red);
		g.setFont(new Font("Ink Free",Font.BOLD,40));
		FontMetrics metrics1 = getFontMetrics(g.getFont());
		g.drawString("Score: " + applesEaten, (SCREEN_WIDTH - metrics1.stringWidth("Score: " + applesEaten))/2, g.getFont().getSize());
		
		//Game Over Text
		g.setColor(Color.red);
		g.setFont(new Font("Ink Free",Font.BOLD,75));
		FontMetrics metrics2 = getFontMetrics(g.getFont());
		g.drawString("GAME OVER", (SCREEN_WIDTH - metrics2.stringWidth("GAME OVER"))/2, SCREEN_HEIGHT/2);
		
		//Game Restart Text
		g.setColor(Color.green);
		g.setFont(new Font("Ink Free",Font.BOLD,20));
		FontMetrics metrics3 = getFontMetrics(g.getFont());
		g.drawString("Press Space to Restart", (SCREEN_WIDTH - metrics3.stringWidth("Press Space to Restart"))/2, SCREEN_HEIGHT/2+50);
		
		//Game Exit Text
		g.setColor(Color.red);
		g.setFont(new Font("Ink Free",Font.BOLD,20));
		FontMetrics metrics4 = getFontMetrics(g.getFont());
		g.drawString("Press Esc to Exit", (SCREEN_WIDTH - metrics4.stringWidth("Press Esc to Exit"))/2, SCREEN_HEIGHT/2+100);
	}
	
	@Override
	public void actionPerformed(ActionEvent e) 
	{
		//If program is running
		if(running)
		{
			move();
			checkApple();
			checkCollisions();
		}
		repaint();
	}
	public class MyKeyAdapter extends KeyAdapter
	{
		@Override
		//Checks for the Arrow key pressed
		//Ensures that the Player cannot run directly into themselves
		public void keyPressed(KeyEvent e)
		{
			switch(e.getKeyCode())
			{
				case KeyEvent.VK_LEFT:
					if(direction != 'R')
						direction = 'L';
					break;
				case KeyEvent.VK_RIGHT:
					if(direction != 'L')
						direction = 'R';
					break;
				case KeyEvent.VK_UP:
					if(direction != 'D')
						direction = 'U';
					break;
				case KeyEvent.VK_DOWN:
					if(direction != 'U')
						direction = 'D';
					break;
				case KeyEvent.VK_SPACE:
					closeRunningWindow(e);
					new GameFrame();
					break;	
				case KeyEvent.VK_ESCAPE:
					System.exit(0);
					break;			
			}
		}

		private void closeRunningWindow(KeyEvent e) 
		{
			JComponent comp = (JComponent) e.getSource();
	        Window win = SwingUtilities.getWindowAncestor(comp);
	        win.dispose();
		}
	}

}
