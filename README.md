 # Incident_Evidence_Management_System
--PL/SQL project designed to collect evidence and incident information related to specific types of attacks, with the ability to add and modify them.
-- =========================
--        D D L
-- =========================
-- Drop tables if they exist (pour réinitialiser)
DROP TABLE ACTION_LOG CASCADE CONSTRAINTS;
DROP TABLE EVIDENCE CASCADE CONSTRAINTS;
DROP TABLE INCIDENTS CASCADE CONSTRAINTS;
DROP TABLE USERS CASCADE CONSTRAINTS;
DROP TABLE DEPARTMENT CASCADE CONSTRAINTS;

--TABLE DEPARTMENT:
CREATE TABLE DEPARTMENT (
    id_department NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name_department VARCHAR2(50) NOT NULL,
    manager_name VARCHAR2(50)
);
--TABLE USERS:
CREATE TABLE USERS (
    userid NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    name VARCHAR2(50) NOT NULL, 
    role VARCHAR2(50) NOT NULL CHECK (role IN ('ADMIN', 'ANALYST', 'VIEWER')),
    email VARCHAR2(100) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT SYSDATE,
    department_id NUMBER,
    CONSTRAINT fk_user_dept FOREIGN KEY (department_id) 
        REFERENCES DEPARTMENT(id_department)
        ON DELETE SET NULL
);
--TABLE INCIDENTS
CREATE TABLE INCIDENTS (
    incidentid NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    title VARCHAR2(200) NOT NULL,
    severity_level VARCHAR2(50) CHECK (severity_level IN ('CRITICAL', 'HIGH', 'MEDIUM', 'LOW')),
    date_reporting TIMESTAMP DEFAULT SYSDATE,
    resolved_at TIMESTAMP NULL,
    reported_by NUMBER,
    CONSTRAINT fk_incident_reporter FOREIGN KEY (reported_by) 
        REFERENCES USERS(userid)
        ON DELETE SET NULL
);
-- TABLE EVIDENCE:
CREATE TABLE EVIDENCE (
    evidence_id NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    incident_id NUMBER NOT NULL,
    evidence_type VARCHAR2(100) NOT NULL,
    collected_at TIMESTAMP DEFAULT SYSDATE,
    collected_by NUMBER,
    CONSTRAINT fk_evidence_incident FOREIGN KEY (incident_id) 
        REFERENCES INCIDENTS(incidentid)
        ON DELETE CASCADE,
    CONSTRAINT fk_evidence_collector FOREIGN KEY (collected_by) 
        REFERENCES USERS(userid)
        ON DELETE SET NULL
);
--TABLE ACTION_LOG
CREATE TABLE ACTION_LOG (
    log_id NUMBER GENERATED ALWAYS AS IDENTITY PRIMARY KEY,
    incident_id NUMBER NOT NULL,
    user_id NUMBER,
    action_type VARCHAR2(100) NOT NULL,
    action_description CLOB,
    action_date TIMESTAMP DEFAULT SYSDATE,
    CONSTRAINT fk_log_incident FOREIGN KEY (incident_id) 
        REFERENCES INCIDENTS(incidentid)
        ON DELETE CASCADE,
    CONSTRAINT fk_log_user FOREIGN KEY (user_id) 
        REFERENCES USERS(userid)
        ON DELETE SET NULL
);
-- =========================
--        D D L
-- =========================


-- =========================
--        INDEX (Performance Optimization)
-- =========================

CREATE INDEX idx_incidents_severity ON INCIDENTS(severity_level);
CREATE INDEX idx_incidents_reported ON INCIDENTS(reported_by);
CREATE INDEX idx_incidents_date ON INCIDENTS(date_reporting);
CREATE INDEX idx_users_department ON USERS(department_id);
CREATE INDEX idx_users_role ON USERS(role);
CREATE INDEX idx_evidence_incident ON EVIDENCE(incident_id);
CREATE INDEX idx_evidence_collector ON EVIDENCE(collected_by);
CREATE INDEX idx_action_incident ON ACTION_LOG(incident_id);
CREATE INDEX idx_action_user ON ACTION_LOG(user_id);
CREATE INDEX idx_action_date ON ACTION_LOG(action_date);

-- =========================
--        DML (Data Manipulation Language)
-- =========================

-- INSERTION DES DÉPARTEMENTS
INSERT INTO DEPARTMENT (name_department, manager_name) VALUES
('Cybersecurity', 'Rayhana Laznasni');

INSERT INTO DEPARTMENT (name_department, manager_name) VALUES
('IT Support', 'Othmane Laarich');

INSERT INTO DEPARTMENT (name_department, manager_name) VALUES
('Network Operations', 'Mohammed Khamrich');

COMMIT;

-- INSERTION DES UTILISATEURS

-- Département Cybersecurity (10 utilisateurs)
INSERT INTO USERS (name, role, email, department_id) VALUES
('Rania Rameh', 'ADMIN', 'rania.rameh@company.com', 1);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Amine Allay', 'ANALYST', 'amine.allay@company.com', 1);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Salma Fethioui', 'ANALYST', 'salma.fethioui@company.com', 1);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Khalid Adama', 'ANALYST', 'khalid.adama@company.com', 1);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Lina Chardia', 'ANALYST', 'lina.chardia@company.com', 1);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Ammar Elmeliani', 'ANALYST', 'ammar.elmeliani@company.com', 1);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Aya Ayayay', 'ANALYST', 'aya.ayayay@company.com', 1);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Yassir Jaber', 'VIEWER', 'yassir.jaber@company.com', 1);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Adam Limouni', 'VIEWER', 'adam.limouni@company.com', 1);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Reda Meskouri', 'ANALYST', 'reda.meskouri@company.com', 1);

-- Département IT Support (7 utilisateurs)
INSERT INTO USERS (name, role, email, department_id) VALUES
('Youssef Amrani', 'ADMIN', 'youssef.amrani@company.ma', 2);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Salma Benali', 'ANALYST', 'salma.benali@company.ma', 2);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Hamza Ait Lahcen', 'ANALYST', 'hamza.aitlahcen@company.ma', 2);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Imane Idrissi', 'ANALYST', 'imane.idrissi@company.ma', 2);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Omar Boulahcen', 'VIEWER', 'omar.boulahcen@company.ma', 2);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Nour El Fassi', 'ANALYST', 'nour.elfassi@company.ma', 2);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Anas Zerktouni', 'VIEWER', 'anas.zerktouni@company.ma', 2);

-- Département Network Operations (3 utilisateurs)
INSERT INTO USERS (name, role, email, department_id) VALUES
('Khalid El Mansouri', 'ADMIN', 'khalid.elmansouri@company.ma', 3);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Sara Ouali', 'ANALYST', 'sara.ouali@company.ma', 3);
INSERT INTO USERS (name, role, email, department_id) VALUES
('Yassin Amrani', 'ANALYST', 'yassin.amrani@company.ma', 3);

COMMIT;

-- INSERTION DES INCIDENTS (15 INCIDENTS)
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('SQL Injection Attempt Detected', 'CRITICAL', 2);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Unauthorized Access to Admin Panel', 'CRITICAL', 3);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('DDoS Attack on Web Server', 'HIGH', 4);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Malware Detected on Workstation', 'HIGH', 5);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Phishing Email Campaign', 'MEDIUM', 6);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Brute Force Login Attempts', 'HIGH', 2);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Data Exfiltration Suspected', 'CRITICAL', 3);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Ransomware Attack', 'CRITICAL', 4);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Port Scanning Activity', 'MEDIUM', 7);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Suspicious Network Traffic', 'MEDIUM', 19);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Compromised User Account', 'HIGH', 5);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Insider Threat Activity', 'HIGH', 6);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Zero-Day Exploit Attempt', 'CRITICAL', 2);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Cross-Site Scripting (XSS)', 'MEDIUM', 3);
INSERT INTO INCIDENTS (title, severity_level, reported_by) VALUES
('Password Policy Violation', 'LOW', 11);

COMMIT;

-- =========================
--        TRIGGER (Validation Cybersecurity)
-- =========================

-- Trigger pour restreindre la collecte de preuves au département Cybersecurity uniquement
CREATE OR REPLACE TRIGGER trg_check_cyber_collector
BEFORE INSERT ON EVIDENCE
FOR EACH ROW
DECLARE
    v_count NUMBER;
BEGIN
    SELECT COUNT(*)
    INTO v_count
    FROM USERS u
    JOIN DEPARTMENT d ON u.department_id = d.id_department
    WHERE u.userid = :NEW.collected_by
      AND d.name_department = 'Cybersecurity';
      IF v_count = 0 THEN
        RAISE_APPLICATION_ERROR(-20001, 
            'ERROR: Only Cybersecurity department users can collect evidence!');
    END IF;
END;
/

-- Trigger pour logger automatiquement l'ajout de preuves
CREATE OR REPLACE TRIGGER trg_log_evidence_added
AFTER INSERT ON EVIDENCE
FOR EACH ROW
BEGIN
    INSERT INTO ACTION_LOG (incident_id, user_id, action_type, action_description)
    VALUES (:NEW.incident_id, :NEW.collected_by, 'EVIDENCE_ADDED', 
            'Evidence of type "' || :NEW.evidence_type || '" was added');
END;
/

-- INSERTION DES PREUVES (avec validation automatique par trigger)

-- Evidence pour Incident 1 (SQL Injection)
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(1, 'LOG - Web Application Firewall', 2);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(1, 'NETWORK - Packet Capture', 3);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(1, 'FILE - Malicious Query Log', 4);

-- Evidence pour Incident 2 (Unauthorized Access)
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(2, 'LOG - Authentication Logs', 3);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(2, 'NETWORK - IP Address Trace', 5);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(2, 'SCREENSHOT - Unauthorized Access Attempt', 6);

-- Evidence pour Incident 3 (DDoS Attack)
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(3, 'NETWORK - Traffic Analysis', 3);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(3, 'LOG - Server Logs', 2);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(3, 'FILE - DDoS Pattern Report', 9);

-- Evidence pour Incident 4 (Malware)
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(4, 'FILE - Malware Sample', 5);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(4, 'LOG - Antivirus Detection Log', 6);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(4, 'SCREENSHOT - Infected System', 2);

-- Evidence pour Incident 5 (Phishing)
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(5, 'FILE - Phishing Email', 6);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(5, 'NETWORK - Email Header Analysis', 7);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(5, 'SCREENSHOT - Fake Login Page', 3);

-- Evidence pour Incident 6 (Brute Force)
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(6, 'LOG - Failed Login Attempts', 2);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(6, 'NETWORK - Source IP Analysis', 3);

-- Evidence pour Incident 7 (Data Exfiltration)
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(7, 'NETWORK - Outbound Traffic Spike', 9);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(7, 'LOG - File Access Logs', 3);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(7, 'FILE - Suspicious Data Transfer', 4);

-- Evidence pour Incident 8 (Ransomware)
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(8, 'FILE - Ransomware Binary', 4);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(8, 'SCREENSHOT - Ransom Note', 5);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(8, 'LOG - Encryption Activity Log', 6);

-- Evidence pour Incident 9 (Port Scanning)
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(9, 'NETWORK - Port Scan Detection', 1);
INSERT INTO EVIDENCE (incident_id, evidence_type, collected_by) VALUES
(9, 'LOG - Firewall Logs', 7);

COMMIT;

-- Actions manuelles supplémentaires
INSERT INTO ACTION_LOG (incident_id, user_id, action_type, action_description) VALUES
(1, 2, 'CREATED', 'Incident created - SQL Injection detected');
INSERT INTO ACTION_LOG (incident_id, user_id, action_type, action_description) VALUES
(2, 3, 'CREATED', 'Incident created - Unauthorized access attempt');
INSERT INTO ACTION_LOG (incident_id, user_id, action_type, action_description) VALUES
(3, 4, 'CREATED', 'Incident created - DDoS attack ongoing');
INSERT INTO ACTION_LOG (incident_id, user_id, action_type, action_description) VALUES
(8, 1, 'ESCALATION', 'CRITICAL: Ransomware - Escalated to management');

COMMIT;
- =========================
--        PL/SQL - BLOCS ANONYMES
-- =========================

-- Bloc 1: Afficher les incidents critiques
SET SERVEROUTPUT ON;

BEGIN
    DBMS_OUTPUT.PUT_LINE('========================================');
    DBMS_OUTPUT.PUT_LINE('   INCIDENTS CRITIQUES NON RESOLUS');
    DBMS_OUTPUT.PUT_LINE('========================================');
    
   FOR rec IN (
        SELECT i.incidentid, i.title, u.name AS reporter
        FROM INCIDENTS i
        JOIN USERS u ON i.reported_by = u.userid
        WHERE i.severity_level = 'CRITICAL' AND i.resolved_at IS NULL
        ORDER BY i.date_reporting DESC
    ) LOOP
        DBMS_OUTPUT.PUT_LINE('ID: ' || rec.incidentid || 
                           ' | Title: ' || rec.title || 
                           ' | Reporter: ' || rec.reporter);
   END LOOP;
END;
/

-- Bloc 2: Vérifier et afficher les utilisateurs Cybersecurity
BEGIN
    DBMS_OUTPUT.PUT_LINE('========================================');
    DBMS_OUTPUT.PUT_LINE('   CYBERSECURITY TEAM MEMBERS');
    DBMS_OUTPUT.PUT_LINE('========================================');
    
   FOR rec IN (
        SELECT u.userid, u.name, u.role, u.email
        FROM USERS u
        JOIN DEPARTMENT d ON u.department_id = d.id_department
        WHERE d.name_department = 'Cybersecurity'
        ORDER BY u.role, u.name
   ) LOOP
        DBMS_OUTPUT.PUT_LINE('ID: ' || rec.userid || 
                           ' | Name: ' || rec.name || 
                           ' | Role: ' || rec.role ||
                           ' | Email: ' || rec.email);
    END LOOP;
END;
/

-- Bloc 3: Statistiques globales
DECLARE
    v_total_incidents NUMBER;
    v_critical_incidents NUMBER;
    v_total_evidence NUMBER;
    v_total_users NUMBER;
BEGIN
    SELECT COUNT(*) INTO v_total_incidents FROM INCIDENTS;
    SELECT COUNT(*) INTO v_critical_incidents FROM INCIDENTS 
        WHERE severity_level = 'CRITICAL';
    SELECT COUNT(*) INTO v_total_evidence FROM EVIDENCE;
    SELECT COUNT(*) INTO v_total_users FROM USERS;
    
   DBMS_OUTPUT.PUT_LINE('========================================');
    DBMS_OUTPUT.PUT_LINE('   SYSTEM STATISTICS');
    DBMS_OUTPUT.PUT_LINE('========================================');
    DBMS_OUTPUT.PUT_LINE('Total Incidents: ' || v_total_incidents);
    DBMS_OUTPUT.PUT_LINE('Critical Incidents: ' || v_critical_incidents);
    DBMS_OUTPUT.PUT_LINE('Total Evidence: ' || v_total_evidence);
    DBMS_OUTPUT.PUT_LINE('Total Users: ' || v_total_users);
END;
/
-- cursor: Statistiques par département
DECLARE
    CURSOR c_stats IS
        SELECT d.name_department,
               d.manager_name,
               COUNT(DISTINCT u.userid) AS total_users,
               COUNT(DISTINCT i.incidentid) AS incidents_reported,
               COUNT(DISTINCT e.evidence_id) AS evidence_collected
        FROM DEPARTMENT d
        LEFT JOIN USERS u ON u.department_id = d.id_department
        LEFT JOIN INCIDENTS i ON i.reported_by = u.userid
        LEFT JOIN EVIDENCE e ON e.collected_by = u.userid
        GROUP BY d.id_department, d.name_department, d.manager_name
        ORDER BY incidents_reported DESC;
BEGIN
    FOR r IN c_stats LOOP
        DBMS_OUTPUT.PUT_LINE(
            r.name_department || ' | Manager: ' || r.manager_name ||
            ' | Users: ' || r.total_users ||
            ' | Incidents: ' || r.incidents_reported ||
            ' | Evidence: ' || r.evidence_collected
        );
    END LOOP;
END;
/
----- Curseur: Parcourir les preuves d'un incident----
DECLARE
    CURSOR c_evidence (p_incident_id NUMBER) IS
        SELECT e.evidence_type, e.collected_at,
               u.name AS collector_name, d.name_department
        FROM EVIDENCE e
        JOIN USERS u ON e.collected_by = u.userid
        JOIN DEPARTMENT d ON u.department_id = d.id_department
        WHERE e.incident_id = p_incident_id
        ORDER BY e.collected_at;
    
    v_incident_title INCIDENTS.title%TYPE;
BEGIN
    -- Obtenir le titre de l'incident
    SELECT title INTO v_incident_title FROM INCIDENTS WHERE incidentid = 1;
    
   DBMS_OUTPUT.PUT_LINE('========================================');
    DBMS_OUTPUT.PUT_LINE('EVIDENCE FOR: ' || v_incident_title);
    DBMS_OUTPUT.PUT_LINE('========================================');
    
   FOR rec IN c_evidence(1) LOOP
        DBMS_OUTPUT.PUT_LINE('Type: ' || rec.evidence_type);
        DBMS_OUTPUT.PUT_LINE('Collected by: ' || rec.collector_name || 
                           ' (' || rec.name_department || ')');
        DBMS_OUTPUT.PUT_LINE('Date: ' || TO_CHAR(rec.collected_at, 'DD-MON-YYYY HH24:MI'));
        DBMS_OUTPUT.PUT_LINE('----------------------------------------');
    END LOOP;
END;
/
