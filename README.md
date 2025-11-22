# GUI
package StudentFormProject;

import java.awt.Color;
import java.awt.Component;
import java.awt.Font;
import java.awt.Graphics;
import java.awt.Image;
import java.awt.Rectangle;
import java.awt.Cursor;
import java.awt.event.FocusEvent;
import java.awt.event.FocusListener;
import javax.swing.ImageIcon;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JPanel;
import javax.swing.JTextField;
import javax.swing.JComboBox;
import javax.swing.JList;
import javax.swing.JButton;
import javax.swing.DefaultListCellRenderer;
import javax.swing.plaf.basic.BasicComboBoxUI; 
import javax.swing.plaf.basic.BasicComboPopup;
import javax.swing.plaf.basic.ComboPopup;
import java.util.Vector;

public class StudentForm {
    public static void main(String[] args) {

        JFrame frame = new JFrame();
        frame.setTitle("Student Form");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(640, 400);
        frame.setResizable(true);
        frame.setExtendedState(JFrame.MAXIMIZED_BOTH);

        ImageIcon bg = new ImageIcon("src/StudentFormProject/page_1.png");
        Image bgImage = bg.getImage();
        ImageIcon logo = new ImageIcon("src/StudentFormProject/star_on.png");
        frame.setIconImage(logo.getImage());

        JPanel bgPanel = new JPanel() {
            @Override
            protected void paintComponent(Graphics g) {
                super.paintComponent(g);
                g.drawImage(bgImage, 0, 0, getWidth(), getHeight(), this);
            }
        };

        bgPanel.setLayout(null);
        frame.setContentPane(bgPanel);

        // ---------------------------------------------------------
        // 1. CREATE TOP 3 TEXT FIELDS (WITH PLACEHOLDERS)
        // ---------------------------------------------------------
        JTextField[] fields = new JTextField[3];
        String[] textPlaceholders = {"First Name", "Middle Initial", "Last Name"};

        for (int i = 0; i < 3; i++) {
            fields[i] = new JTextField(textPlaceholders[i]);
            fields[i].setOpaque(false);
            fields[i].setBorder(null);
            fields[i].setCaretColor(Color.WHITE);
            fields[i].setForeground(Color.GRAY); 

            final int index = i;
            fields[i].addFocusListener(new FocusListener() {
                @Override
                public void focusGained(FocusEvent e) {
                    if (fields[index].getForeground() == Color.GRAY) {
                        fields[index].setText("");
                        fields[index].setForeground(Color.WHITE);
                    }
                }
                @Override
                public void focusLost(FocusEvent e) {
                    if (fields[index].getText().isEmpty()) {
                        fields[index].setText(textPlaceholders[index]);
                        fields[index].setForeground(Color.GRAY);
                    }
                }
            });
            bgPanel.add(fields[i]);
        }

        // ---------------------------------------------------------
        // 2. CREATE DATE OPTION BOXES (DD/MM/YY = GRAY, OPTIONS = WHITE)
        // ---------------------------------------------------------
        
        // 1. Generate Numbers Only
        Vector<String> days = new Vector<>();
        for(int i=1; i<=31; i++) days.add(String.format("%02d", i));

        Vector<String> months = new Vector<>();
        for(int i=1; i<=12; i++) months.add(String.format("%02d", i));

        Vector<String> years = new Vector<>();
        for(int i=2024; i>=1980; i--) years.add(String.valueOf(i));

        // 2. Create Boxes
        JComboBox<String> dayBox = new JComboBox<>(days);
        JComboBox<String> monthBox = new JComboBox<>(months);
        JComboBox<String> yearBox = new JComboBox<>(years);
        
        JComboBox<String>[] dateBoxes = new JComboBox[]{dayBox, monthBox, yearBox};
        String[] boxPlaceholders = {"DD", "MM", "YYYY"};

        for (int i = 0; i < 3; i++) {
            JComboBox<String> box = dateBoxes[i];
            
            // Capture the specific placeholder ("DD", "MM", or "YYYY")
            final String placeholder = boxPlaceholders[i]; 

            bgPanel.add(box);

            // IMPORTANT: Set to NULL so the renderer knows to show the placeholder
            box.setSelectedItem(null);
            

            // Basic invisible style
            box.setBorder(null);
            box.setFocusable(false);
            box.setOpaque(false);
            box.setForeground(Color.WHITE);

            // UI Override (Remove arrow, etc)
            box.setUI(new BasicComboBoxUI() {
                @Override protected JButton createArrowButton() {
                    return new JButton() {
                        @Override public int getWidth() { return 0; }
                        @Override public boolean isVisible() { return false; }
                    };
                }
                @Override public void paintCurrentValueBackground(Graphics g, Rectangle bounds, boolean hasFocus) {}
                
                @Override protected ComboPopup createPopup() {
                    BasicComboPopup popup = new BasicComboPopup(comboBox);
                    popup.getList().setBackground(Color.BLACK); // Popup Background
                    popup.getList().setForeground(Color.WHITE); // Popup Text Color
                    popup.getList().setSelectionBackground(Color.DARK_GRAY);
                    popup.getList().setSelectionForeground(Color.WHITE);
                    return popup;
                }
            });

            // --- CUSTOM RENDERER (THIS DOES THE COLOR LOGIC) ---
            box.setRenderer(new DefaultListCellRenderer() {
                @Override
                public Component getListCellRendererComponent(JList<?> list, Object value, int index, boolean isSelected, boolean cellHasFocus) {
                    super.getListCellRendererComponent(list, value, index, isSelected, cellHasFocus);
                    setFont(box.getFont()); 

                    // --- MAIN BOX VIEW (index -1) ---
                    if (index == -1) {
                        setOpaque(false);
                        
                        if (value == null) {
                            // CASE 1: Nothing selected yet
                            setText(placeholder);      // Show "DD", "MM", etc.
                            setForeground(Color.GRAY); // Make it GRAY
                        } else {
                            // CASE 2: User picked a number
                            setForeground(Color.WHITE); // Make it WHITE
                        }
                    } 
                    // --- DROPDOWN LIST VIEW (index >= 0) ---
                    else {
                        setOpaque(true);
                        setForeground(Color.WHITE); // Options inside the list are always WHITE

                        if (isSelected) {
                            setBackground(Color.DARK_GRAY);
                        } else {
                            setBackground(Color.BLACK);
                        }
                    }
                    return this;
                }
            });
        }

        // ---------------------------------------------------------
        // RELATIVE POSITIONS
        // ---------------------------------------------------------
        double[][] box = {
            //  xPos     yPos      width     height
            {0.13, 0.387, 0.23, 0.06},    // First Name
            {0.393, 0.387, 0.23, 0.06},   // Middle Initial
            {0.656, 0.387, 0.23, 0.06},   // Last Name
            
            {0.13, 0.682,  0.115, 0.06},  
            {0.28, 0.682,  0.115, 0.06},  
            {0.43, 0.682,  0.115, 0.06},  
        };

        // ---------------------------------------------------------
        // RESIZE HANDLER
        // ---------------------------------------------------------
        frame.addComponentListener(new java.awt.event.ComponentAdapter() {
            @Override
            public void componentResized(java.awt.event.ComponentEvent e) {

                int w = bgPanel.getWidth();
                int h = bgPanel.getHeight();
                int fontSize = (int)(h * 0.03); 
                Font dynamicFont = new Font("Roboto", Font.PLAIN, fontSize);

                for (int i = 0; i < 3; i++) {
                    int x = (int)(w * box[i][0]);
                    int y = (int)(h * box[i][1]);
                    int fw = (int)(w * box[i][2]);
                    int fh = (int)(h * box[i][3]);

                    fields[i].setBounds(x, y, fw, fh);
                    fields[i].setFont(dynamicFont);
                }

                for (int i = 0; i < 3; i++) {
                    int x = (int)(w * box[i + 3][0]);
                    int y = (int)(h * box[i + 3][1]);
                    int fw = (int)(w * box[i + 3][2]);
                    int fh = (int)(h * box[i + 3][3]);

                    dateBoxes[i].setBounds(x, y, fw, fh);
                    dateBoxes[i].setFont(dynamicFont); 
                }

                bgPanel.repaint();
            }
        });
        
        // ---------------------------------------------------------
        // BUTTON SYSTEM
        // ---------------------------------------------------------
        ImageIcon btnOff = new ImageIcon("src/StudentFormProject/button_space.png");
        ImageIcon btnOn  = new ImageIcon("src/StudentFormProject/button_selected.png");

        JLabel[] btn = { new JLabel(), new JLabel(), new JLabel() };
        for (JLabel b : btn) bgPanel.add(b);
     
        double[][] btnPos = {
            {0.1135, 0.541, 0.022, 0.04125},
            {0.38, 0.541, 0.022, 0.04125},
            {0.685, 0.541, 0.022, 0.04125}
        };
     
        for (JTextField f : fields) f.setCursor(new Cursor(Cursor.TEXT_CURSOR));
        for (JLabel b : btn) b.setCursor(new Cursor(Cursor.HAND_CURSOR));
        for (JComboBox<String> b : dateBoxes) b.setCursor(new Cursor(Cursor.HAND_CURSOR));


        java.util.function.BiFunction<ImageIcon, int[], ImageIcon> scale = (icon, size) ->
            new ImageIcon(icon.getImage().getScaledInstance(size[0], size[1], Image.SCALE_SMOOTH));

        int[] selectedIndex = { -1 };

        frame.addComponentListener(new java.awt.event.ComponentAdapter() {
            @Override
            public void componentResized(java.awt.event.ComponentEvent e) {
                int w = bgPanel.getWidth();
                int h = bgPanel.getHeight();

                for (int i = 0; i < 3; i++) {
                    int bw = (int)(w * btnPos[i][2]);
                    int bh = (int)(h * btnPos[i][3]);
                    int bx = (int)(w * btnPos[i][0]);
                    int by = (int)(h * btnPos[i][1]);

                    btn[i].setBounds(bx, by, bw, bh);

                    boolean isSelected = (selectedIndex[0] == i);
                    btn[i].setIcon(scale.apply(isSelected ? btnOn : btnOff, new int[]{bw, bh}));
                }
            }
        });

        for (int i = 0; i < 3; i++) {
            final int index = i;
            btn[i].addMouseListener(new java.awt.event.MouseAdapter() {
                @Override
                public void mouseClicked(java.awt.event.MouseEvent e) {
                    selectedIndex[0] = index;
                    frame.dispatchEvent(new java.awt.event.ComponentEvent(frame,
                            java.awt.event.ComponentEvent.COMPONENT_RESIZED));
                }
            });
        }

        frame.setVisible(true);
        javax.swing.SwingUtilities.invokeLater(() -> frame.requestFocusInWindow());

    }
}
