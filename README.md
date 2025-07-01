# -
五子棋游戏的设计
import javax.swing.*;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.util.ArrayList;
import java.util.List;

public class wuziqi extends JFrame {
	private static final long serialVersionUID = 1L;
	private static final int ROWS = 15, COLS = 15;
    private static final int CELL = 30, MARGIN = 20;

    private int[][] board = new int[ROWS][COLS];
    private List<Point> black = new ArrayList<>(), white = new ArrayList<>();
    private ChessPanel panel;

    public wuziqi() {
        setTitle("五子棋（精准落子版）");
        setSize(COLS * CELL + 2 * MARGIN, ROWS * CELL + 2 * MARGIN);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setLocationRelativeTo(null);

        panel = new ChessPanel();
        panel.addMouseListener(new MouseAdapter() {
            public void mouseClicked(MouseEvent e) {
                int rawX = e.getX();
                int rawY = e.getY();
                int col = (rawX - MARGIN + CELL/2) / CELL;
                int row = (rawY - MARGIN + CELL/2) / CELL;

                if (col < 0 || col >= COLS || row < 0 || row >= ROWS || board[row][col] != 0) {
                    return;
                }

                if (SwingUtilities.isLeftMouseButton(e)) {
                    board[row][col] = 1;
                    black.add(new Point(col, row));
                    if (checkWin(1, col, row)) {
                        endGame("黑棋胜！");
                    }
                } else {
                    board[row][col] = 2;
                    white.add(new Point(col, row));
                    if (checkWin(2, col, row)) {
                        endGame("白棋胜！");
                    }
                }
                panel.repaint();
            }
        });
        add(panel);
    }

    private boolean checkWin(int player, int x, int y) {
        int[][] dirs = {{-1,0},{1,0},{0,-1},{0,1},{-1,-1},{-1,1},{1,-1},{1,1}};
        for (int[] d : dirs) {
            int count = 1;
            for (int s = 1; s < 5; s++) {
                int nx = x + d[0]*s;
                int ny = y + d[1]*s;
                if (nx<0||nx>=COLS||ny<0||ny>=ROWS||board[ny][nx]!=player) break;
                count++;
            }
            for (int s = 1; s < 5; s++) {
                int nx = x - d[0]*s;
                int ny = y - d[1]*s;
                if (nx<0||nx>=COLS||ny<0||ny>=ROWS||board[ny][nx]!=player) break;
                count++;
            }
            if (count >= 5) return true;
        }
        return false;
    }

    private void endGame(String msg) {
        JOptionPane.showMessageDialog(this, msg);
        board = new int[ROWS][COLS];
        black.clear();
        white.clear();
        panel.repaint();
    }

	class ChessPanel extends JPanel {
		private static final long serialVersionUID = 1L;

		protected void paintComponent(Graphics g) {
            super.paintComponent(g);
            for (int i = 0; i < ROWS; i++) {
                for (int j = 0; j < COLS; j++) {
                    g.drawRect(MARGIN + j*CELL, MARGIN + i*CELL, CELL, CELL);
                }
            }
            for (Point p : black) {
                g.setColor(Color.BLACK);
                g.fillOval(MARGIN + p.x*CELL - 12, MARGIN + p.y*CELL - 12, 24, 24);
            }
            for (Point p : white) {
                g.setColor(Color.WHITE);
                g.fillOval(MARGIN + p.x*CELL - 12, MARGIN + p.y*CELL - 12, 24, 24);
                g.setColor(Color.BLACK);
                g.drawOval(MARGIN + p.x*CELL - 12, MARGIN + p.y*CELL - 12, 24, 24);
            }
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            wuziqi game = new wuziqi();
            game.setVisible(true);
        });
    }
}
