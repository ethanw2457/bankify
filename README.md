# peas
public FoundationDatabase(String scpIn, String researcherIn, String databaseOutput) {
        this.scpIn = scpIn;
        this.researcherIn = researcherIn;
        this.databaseOutput = databaseOutput;
        scp = null;
        researcher = null;
    }
   
    public boolean readSCP() {
        try (BufferedReader reader = new BufferedReader(new FileReader(scpIn));
             BufferedReader reader2 = new BufferedReader(new FileReader(scpIn))) {
            String line = reader.readLine();
            int index = 0;
            while (line != null) {
                index++;
                line = reader.readLine();
            }
            scp = new SCP[index];
            line = reader2.readLine();
            index = 0;
            while (line != null) {
                SCP current = null;
                try {
                    current = new SCP(line);
                } catch (BadDataException e) {
                    current = new SCP(e);
                }
                scp[index] = current;
                index++;
                line = reader2.readLine();
            }
        } catch (Exception e) {
            return false;
        }
        return true;
    }
   
    public boolean readResearcher() {
        try (BufferedReader reader = new BufferedReader(new FileReader(researcherIn));
             BufferedReader reader2 = new BufferedReader(new FileReader(researcherIn))) {
            String line = reader.readLine();
            int index = 0;
            while (line != null) {
                index++;
                line = reader.readLine();
            }
            researcher = new Researcher[index];
            line = reader2.readLine();
            index = 0;
            while (line != null) {
                Researcher current = null;
                try {
                    current = new Researcher(line);
                } catch (BadDataException e) {
                    current = new Researcher(e);
                }
                researcher[index] = current;
                index++;
                line = reader2.readLine();
            }
        } catch (Exception e) {
            return false;
        }
        return true;
    }
   
    public boolean autoAssignResearcher() {
        boolean[] assignedResearcher = new boolean[researcher.length];
        boolean[] assignedSCP = new boolean[scp.length];
        for (int i = 0; i < scp.length; i++) {
            SCP currentScp = scp[i];
            for (int j = 0; j < researcher.length; j++) {
                Researcher currentR = researcher[j];
                if (currentR.compatible(currentScp) && !assignedResearcher[j]) {
                    if (currentR.getClearance() == currentScp.getClearanceLevel()) {
                        assignedResearcher[j] = true;
                        assignedSCP[i] = true;
                        Researcher[] passIn = {currentR};
                        currentScp.setResearchers(passIn);
                        break;
                    }
                }
            }
            if (!assignedSCP[i]) {
                for (int j = 0; j < researcher.length; j++) {
                    Researcher currentR = researcher[j];
                    if (!assignedResearcher[j] && currentR.compatible(currentScp)) {
                        assignedResearcher[j] = true;
                        assignedSCP[i] = true;
                        Researcher[] passIn = {currentR};
                        currentScp.setResearchers(passIn);
                        break;
                    }
                }
            }
        }
        for (int i = 0; i < assignedSCP.length; i++) {
            if (!assignedSCP[i] && !scp[i].getObjectName().contains("Bad SCP Data")) {
                return false;
            }
        }
        return true;
    }
   
    public boolean modifyResearcher(String oldData, String newData) {
        try {
            Researcher oldResearcher = new Researcher(oldData);
            for (int i = 0; i < researcher.length; i++) {
                if (researcher[i].equals(oldResearcher)) {
                    researcher[i] = new Researcher(newData);
                    autoAssignResearcher();
                    return true;
                }
            }
        } catch (BadDataException e) {
            return false;
        }
        return false;
    }

    public boolean modifySCP(String oldData, String newData) {
        try {
            SCP oldSCP = new SCP(oldData);
            for (int i = 0; i < scp.length; i++) {
                if (scp[i].equals(oldSCP)) {
                    scp[i] = new SCP(newData);
                    autoAssignResearcher();
                    return true;
                }
            }
        } catch (BadDataException e) {
            return false;
        }
        return false;
    }
   
    public boolean addResearcher(int itemNumber, String data) {
        Researcher r;
        try {
            r = new Researcher(data);
        } catch (BadDataException e) {
            return false;  
        }
        boolean inArray = false;
        for (int i = 0; i < researcher.length; i++) {
            if (researcher[i].equals(r)) {
                inArray = true;
                break;
            }
        }
        if (!inArray) {
            Researcher[] savePrev = researcher;
            researcher = new Researcher[savePrev.length + 1];
            for (int i = 0; i < savePrev.length; i++) {
                researcher[i] = savePrev[i];
            }
            researcher[savePrev.length] = r;
        }
        for (int i = 0; i < scp.length; i++) {
            if (scp[i].getItemNumber() == itemNumber) {
                if (r.compatible(scp[i])) {
                    Researcher[] savePrev = scp[i].getResearchers();
                    Researcher[] newArray = new Researcher[savePrev.length + 1];
                    for (int j = 0; j < savePrev.length; j++) {
                        if (r.equals(savePrev[j])) {
                            return false;
                        }
                    }
                    for (int j = 0; j < savePrev.length; j++) {
                        newArray[j] = savePrev[j];
                    }
                    newArray[savePrev.length] = r;
                    scp[i].setResearchers(newArray);
                    return true;
                }
            }
        }
        return false;
    }