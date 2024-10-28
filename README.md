# peas
import java.io.*;
import java.util.ArrayList;
/**
 * A program that sets up the TheSCPFoundation class.
 *
 * Purdue University -- CS18000 -- Fall 2024 -- PJ 03 
 
 * @author Rohan Potta
 * @version October 21, 2024
 */
public class TheSCPFoundation {
    public static void main(String[] args) {
        PrintWriter pw = null;
        
        try (BufferedReader br = new BufferedReader(new FileReader("input.txt"))) {
            String[] fileNames = br.readLine().split(" ");
            String scpInName = fileNames[0];
            String researcherInName = fileNames[1];
            String databaseOutName = fileNames[2];
            String mainOutName = fileNames[3];

            pw = new PrintWriter(new FileWriter(mainOutName));
            FoundationDatabase fd = new FoundationDatabase(scpInName, researcherInName, databaseOutName);
            pw.println("Foundation Database Started");
            ArrayList<String> commands = new ArrayList<String>();
            String line = null;

            while ((line = br.readLine()) != null) {
                commands.add(line);
            }
            for (int i = 0; i < commands.size(); i++) {

                switch (commands.get(i).trim()) {
                    case "1":
                        pw.print("1 ");
                        pw.println(fd.readSCP() ? "Success" : "Failure");
                        break;
                    case "2":
                        pw.print("2 ");
                        pw.println(fd.readResearcher() ? "Success" : "Failure");
                        break;
                    case "3":
                        pw.print("3 ");
                        pw.println(fd.autoAssignResearcher() ? "Success" : "Failure");
                        break;
                    case "4":
                        pw.print("4 ");
                        String arg1 = commands.get(i + 1);
                        String arg2 = commands.get(i + 2);
                        pw.println(fd.modifySCP(arg1, arg2) ? "Success" : "Failure");
                        break;
                    case "5":
                        pw.print("5 ");
                        String arg3 = commands.get(i + 1);
                        String arg4 = commands.get(i + 2);
                        pw.println(fd.modifyResearcher(arg3, arg4) ? "Success" : "Failure");
                        break;
                    case "6":
                        pw.print("6 ");
                        int arg5 = Integer.parseInt(commands.get(i + 1));
                        String arg6 = commands.get(i + 2);
                        pw.println(fd.addResearcher(arg5, arg6) ? "Success" : "Failure");
                        break;
                    case "7":
                        pw.print("7 ");
                        pw.println(fd.outputDatabase() ? "Success" : "Failure");
                        break;
                    default:
                        break;
                }
            }
        } catch (IOException e) {
            if (pw != null) {
                pw.println("IO Read Failure");
            }
        } catch (NumberFormatException e) {
            if (pw != null) {
                pw.println("Command Failure");
            }
        } catch (Exception e) {
            if (pw != null) {
                pw.println("Command Failure");
            }
        } finally {
            if (pw != null) {
                pw.close();
            }
        }
    }