# UI Lab 2 
**Запуск [CLIdemo.java](https://github.com/liketaurus/TUI-Labs/blob/master/Lab%202%20-%20CLI/CLIdemo.java) з цього репозиторію:**
<img src="https://github.com/ppc-ntu-khpi/35-tui-2-Articso/blob/master/images/screen1.png"/>

# Завдання "На п'ять"
>Додайте ще одну команду - report, яка має виводити звіт за клієнтами такого ж виду, як у роботі номер 8 (див. CustomerReport).

**Код файлу** ```CLIdemo.java```**:**  

```java
package com.mybank.tui;

import com.mybank.domain.Bank;
import com.mybank.domain.CheckingAccount;
import com.mybank.domain.Customer;
import com.mybank.domain.SavingsAccount;
import com.mybank.reporting.CustomerReport;

import java.io.PrintWriter;
import java.util.LinkedList;
import java.util.List;

import org.jline.reader.*;
import org.jline.reader.impl.completer.*;
import org.jline.utils.*;
import org.fusesource.jansi.*;

/**
 * Sample application to show how jLine can be used.
 *
 * @author sandarenu
 *
 */
/**
 * Console client for 'Banking' example
 *
 * @author Alexander 'Taurus' Babich
 */
public class CLIdemo {

    public static final String ANSI_RESET = "\u001B[0m";
    public static final String ANSI_BLACK = "\u001B[30m";
    public static final String ANSI_RED = "\u001B[31m";
    public static final String ANSI_GREEN = "\u001B[32m";
    public static final String ANSI_YELLOW = "\u001B[33m";
    public static final String ANSI_BLUE = "\u001B[34m";
    public static final String ANSI_PURPLE = "\u001B[35m";
    public static final String ANSI_CYAN = "\u001B[36m";
    public static final String ANSI_WHITE = "\u001B[37m";

    private String[] commandsList;

    public void init() {
        commandsList = new String[]{"help", "customers", "customer", "report", "exit"};
    }

    public void run() {
        AnsiConsole.systemInstall(); // needed to support ansi on Windows cmd
        printWelcomeMessage();
        LineReaderBuilder readerBuilder = LineReaderBuilder.builder();
        List<Completer> completors = new LinkedList<Completer>();

        completors.add(new StringsCompleter(commandsList));
        readerBuilder.completer(new ArgumentCompleter(completors));

        LineReader reader = readerBuilder.build();

        String line;
        PrintWriter out = new PrintWriter(System.out);

        while ((line = readLine(reader, "")) != null) {
            if ("help".equals(line)) {
                printHelp();
            } else if ("customers".equals(line)) {
                printCustomers();
            } else if (line.startsWith("customer")) {
                printCustomerDetails(line);
            } else if ("report".equals(line)) {
                generateReport();
            } else if ("exit".equals(line)) {
                System.out.println("Exiting application");
                return;
            } else {
                System.out
                        .println(ANSI_RED + "Invalid command, For assistance press TAB or type \"help\" then hit ENTER." + ANSI_RESET);
            }
        }

        AnsiConsole.systemUninstall();
    }

    private void printWelcomeMessage() {
        System.out
                .println("\nWelcome to " + ANSI_GREEN + " MyBank Console Client App" + ANSI_RESET + "! \nFor assistance press TAB or type \"help\" then hit ENTER.");

    }

    private void printHelp() {
        System.out.println("help\t\t\t- Show help");
        System.out.println("customers\t\t- Show list of customers");
        System.out.println("customer \'index\'\t- Show customer details");
        System.out.println("report\t\t\t- Generate customer report");
        System.out.println("exit\t\t\t- Exit the app");

    }

    private void printCustomers() {
        AttributedStringBuilder a = new AttributedStringBuilder()
                .append("\nThis is all of your ")
                .append("customers", AttributedStyle.BOLD.foreground(AttributedStyle.RED))
                .append(":");

        System.out.println(a.toAnsi());
        if (Bank.getNumberOfCustomers() > 0) {
            System.out.println("\nLast name\tFirst Name\tBalance");
            System.out.println("---------------------------------------");
            for (int i = 0; i < Bank.getNumberOfCustomers(); i++) {
                System.out.println(Bank.getCustomer(i).getLastName() + "\t\t" + Bank.getCustomer(i).getFirstName() + "\t\t$" + Bank.getCustomer(i).getAccount(0).getBalance());
            }
        } else {
            System.out.println(ANSI_RED + "Your bank has no customers!" + ANSI_RESET);
        }
    }

    private void printCustomerDetails(String line) {
        try {
            int custNo = 0;
            if (line.length() > 8) {
                String strNum = line.split(" ")[1];
                if (strNum != null) {
                    custNo = Integer.parseInt(strNum);
                }
            }
            Customer cust = Bank.getCustomer(custNo);
            String accType = cust.getAccount(0) instanceof CheckingAccount ? "Checking" : "Savings";

            AttributedStringBuilder a = new AttributedStringBuilder()
                    .append("\nThis is detailed information about customer #")
                    .append(Integer.toString(custNo), AttributedStyle.BOLD.foreground(AttributedStyle.RED))
                    .append("!");

            System.out.println(a.toAnsi());

            System.out.println("\nLast name\tFirst Name\tAccount Type\tBalance");
            System.out.println("-------------------------------------------------------");
            System.out.println(cust.getLastName() + "\t\t" + cust.getFirstName() + "\t\t" + accType + "\t$" + cust.getAccount(0).getBalance());
        } catch (Exception e) {
            System.out
                    .println(ANSI_RED + "ERROR! Wrong customer number!" + ANSI_RESET);
        }
    }

    private void generateReport() {
        CustomerReport report = new CustomerReport();
        report.generateReport();
    }

    private String readLine(LineReader reader, String promtMessage) {
        try {
            String line = reader.readLine(promtMessage + ANSI_YELLOW + "\nbank> " + ANSI_RESET);
            return line.trim();
        } catch (UserInterruptException e) {
            // e.g. ^C
            return null;
        } catch (EndOfFileException e) {
            // e.g. ^D
            return null;
        }
    }

    public static void main(String[] args) {

        Bank.addCustomer("John", "Doe");
        Bank.addCustomer("Fox", "Mulder");
        Bank.getCustomer(0).addAccount(new CheckingAccount(2000));
        Bank.getCustomer(1).addAccount(new SavingsAccount(1000, 3));

        CLIdemo shell = new CLIdemo();
        shell.init();
        shell.run();
    }
}
```

***Резльтат виконання програми:***  

<img src="https://github.com/ppc-ntu-khpi/35-tui-2-Articso/blob/master/images/screen2.png"/>

![](https://img.shields.io/badge/Made%20with-JAVA-red.svg)
![](https://img.shields.io/badge/Made%20with-%20Netbeans-brightgreen.svg)
![](https://img.shields.io/badge/Made%20at-PPC%20NTU%20%22KhPI%22-blue.svg) 
[![Gitter](https://badges.gitter.im/PPC-SE-2020/OOP.svg)](https://gitter.im/PPC-SE-2020/OOP?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)
