import java.io.*;
import java.awt.datatransfer.*;
import java.awt.event.*;
import java.awt.*;

public class Notepad extends Frame {

    Clipboard cBoard = getToolkit().getSystemClipboard();
    TextArea tArea;
    String fName;

    Notepad() {
        gaListener gListener = new gaListener();
        addWindowListener(gListener);

        tArea = new TextArea();
        add(tArea);

        MenuBar mBar = new MenuBar();
        Menu filMenu = new Menu("File");

        MenuItem noption = new MenuItem("New");
        MenuItem ooption = new MenuItem("Open");
        MenuItem soption = new MenuItem("Save");
        MenuItem coption = new MenuItem("Close");

        noption.addActionListener(new Ne_option());
        filMenu.add(noption);

        ooption.addActionListener(new Ope_option());
        filMenu.add(ooption);

        soption.addActionListener(new Sav_option());
        filMenu.add(soption);

        coption.addActionListener(new Clos_option());
        filMenu.add(coption);

        mBar.add(filMenu);

        Menu editMenu = new Menu("Edit");
        MenuItem cutOption = new MenuItem("Cut");
        MenuItem copyOption = new MenuItem("Copy");
        MenuItem pasteOption = new MenuItem("Paste");

        cutOption.addActionListener(new Cu_option());
        editMenu.add(cutOption);

        copyOption.addActionListener(new Cop_option());
        editMenu.add(copyOption);

        pasteOption.addActionListener(new Past_option());
        editMenu.add(pasteOption);

        mBar.add(editMenu);
        setMenuBar(mBar);

        setTitle("Notepad");

    }

    class gaListener extends WindowAdapter {
        public void windowClosing(WindowEvent closeNotepad) {
            System.exit(0);
        }
    }

    class Ne_option implements ActionListener {
        public void actionPerformed(ActionEvent ne) {
            tArea.setText(" ");
        }
    }

    class Ope_option implements ActionListener {
        public void actionPerformed(ActionEvent ope) {
            FileDialog fDialog = new FileDialog(Notepad.this, "select a text file", FileDialog.LOAD);
            fDialog.setVisible(true);

            if (fDialog.getFile() != null) {
                fName = fDialog.getDirectory() + fDialog.getFile();
                setTitle(fName);
                readFile();
            }
            tArea.requestFocus();
        }

        private void readFile() {
            BufferedReader br;
            StringBuilder sBuffer = new StringBuilder();
            try {
                br = new BufferedReader(new FileReader(fName));
                String oLine;

                while ((oLine = br.readLine()) != null)
                    sBuffer.append(oLine).append("\n");
                tArea.setText(sBuffer.toString());
                br.close();
            } catch (FileNotFoundException fe) {
                System.out.print("Required file not found");
            } catch (IOException ioe) {
                ioe.printStackTrace();
            }
        }
    }

    class Clos_option implements ActionListener {
        public void actionPerformed(ActionEvent close_o) {
            System.exit(0);
        }
    }

    class Sav_option implements ActionListener {
        public void actionPerformed(ActionEvent sav_o) {
            FileDialog fDialog = new FileDialog(Notepad.this, "save the text file with .txt extension", FileDialog.SAVE);

            fDialog.setVisible(true);
            if (fDialog.getFile() != null) {
                fName = fDialog.getDirectory() + fDialog.getFile();
                setTitle(fName);
                try {
                    DataOutputStream dOutputStream = new DataOutputStream(new FileOutputStream(fName));
                    String oLine = tArea.getText();
                    BufferedReader bReader = new BufferedReader(new StringReader(oLine));

                    while ((oLine = bReader.readLine()) != null) {
                        dOutputStream.writeBytes(oLine + "\r\n");
                    }
                    dOutputStream.close();
                } catch (Exception ex) {
                    System.out.print("Required file not found");
                }
                tArea.requestFocus();
            }
        }
    }

    class Cu_option implements ActionListener {
        public void actionPerformed(ActionEvent cut_o) {
            String sText = tArea.getSelectedText();
            StringSelection sSelection = new StringSelection(sText);
            cBoard.setContents(sSelection, sSelection);
            tArea.replaceRange(" ", tArea.getSelectionStart(), tArea.getSelectionEnd());
        }
    }

    class Cop_option implements ActionListener {
        public void actionPerformed(ActionEvent copy_o) {
            String sText = tArea.getSelectedText();
            StringSelection cString = new StringSelection(sText);
            cBoard.setContents(cString, cString);

        }
    }

    class Past_option implements ActionListener {
        public void actionPerformed(ActionEvent paste_o) {
            Transferable ctransfer = cBoard.getContents(Notepad.this);
            try {
                String sText = (String) ctransfer.getTransferData(DataFlavor.stringFlavor);
                tArea.replaceRange(sText, tArea.getSelectionStart(), tArea.getSelectionEnd());
            } catch (Exception exc) {
                System.out.println("Not a string flavour");
            }
        }
    }

    public static void main(String args[]) {
        Frame nFrame = new Notepad();
        nFrame.setSize(600, 600);
        nFrame.setVisible(true);
    }
}
