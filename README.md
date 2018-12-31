package com.test.test;

import java.text.DateFormat;
import java.text.ParseException;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
import java.util.Random;

import bsh.Console;

public class genrateTbl {
	static String[] fruits = { "Wating", "Wating", "Wating", "Wating", "Wating", "Wating", "Wating", "Wating", "Wating", "Successful", "Waiting" };

	public static String getJob() {
		int n = 12;
		// chose a Character random from this String
		String AlphaNumericString = "ABCDEFGHI";

		// create StringBuffer size of AlphaNumericString
		StringBuilder sb = new StringBuilder(n);

		for (int i = 0; i < n; i++) {

			// generate a random number between
			// 0 to AlphaNumericString variable length
			int index = (int) (AlphaNumericString.length() * Math.random());

			// add Character one by one in end of sb
			sb.append(AlphaNumericString.charAt(index));
		}

		return sb.toString();
	}

	public static String GetDate() throws ParseException {
		DateFormat formatter = new SimpleDateFormat("MM/dd/yy HH:mm:ss");
		Calendar cal = Calendar.getInstance();
		String str_date1 = "11/06/07 02:10:15";
		String str_date2 = "22/27/07 02:10:20";

		cal.setTime(formatter.parse(str_date1));
		Long value1 = cal.getTimeInMillis();

		cal.setTime(formatter.parse(str_date2));
		Long value2 = cal.getTimeInMillis();

		long value3 = (long) (value1 + Math.random() * (value2 - value1));
		cal.setTimeInMillis(value3);
		return formatter.format(cal.getTime());

	}

	public static String GetStatus() {

		int idx = new Random().nextInt(fruits.length);
		return (fruits[idx]);

	}

	public static void main(String[] args) throws ParseException {

		String sb1 = "<style type=\"text/css\">" + "table.gridtable {" + "	font-family: verdana,arial,sans-serif;"
				+ "	font-size:11px;" + "	color:#333333;" + "	border-width: 1px;" + "	border-color: #666666;"
				+ "	border-collapse: collapse;" + "}" + "table.gridtable th {" + "	border-width: 1px;"
				+ "	padding: 8px;" + "	border-style: solid;" + "	border-color: #666666;"
				+ "	background-color: #dedede;" + "}" + "table.gridtable td {" + "	border-width: 1px;"
				+ "	padding: 8px;" + "	border-style: solid;" + "	border-color: #666666;"
				+ "	background-color: #ffffff;" + "}" + "</style>";
		StringBuilder sb = new StringBuilder();
		sb.append("<html>");
		sb.append("<head>");
		sb.append(sb1);
		sb.append("</head>");
		sb.append("<table class = 'gridtable'>");
		sb.append("<th>Job Id </th>");
		sb.append("<th> Job Name </th>");
		sb.append("<th> Status </th>");
		sb.append("<th> Date </th>");
		int cnt = 0;
		for (int i = 0; i < 5000; i++) {
			cnt++;
			sb.append("<tr>");
			sb.append("<td> " + cnt + " </td>");
			sb.append("<td> " + getJob() + " </td>");
			sb.append("<td> " + GetStatus() + " </td>");
			sb.append("<td> " + GetDate() + " </td>");

			sb.append("</tr>");
		}
		sb.append("</table>");
		sb.append("</body>");
		sb.append("</html>");

		System.out.println(sb.toString());

	}

}
//==========================
package com.test.test;

import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import org.openqa.selenium.JavascriptExecutor;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.WebElement;
import org.openqa.selenium.chrome.ChromeDriver;
import org.openqa.selenium.firefox.FirefoxDriver;

class JobData001 {
	String jobId;
	String jobName;
	String jobStatus;
	String jobDates;

	public JobData001(String prmJobId, String prmJobName, String prmJobStatus, String prmDates) {
		this.jobId = prmJobId;
		this.jobName = prmJobName;
		this.jobStatus = prmJobStatus;
		this.jobDates = prmDates;
	}

}

public class demoSearch {
	WebDriver driver;
	static JavascriptExecutor jsExec;
	static ArrayList<String> listInitialJob = null;
	String searchDate = null;
	static ArrayList<JobData001> listJobResult = null;

	public void setUp() throws Exception {
		System.setProperty("webdriver.chrome.driver",
				"C:\\Users\\MAHESH\\Downloads\\chromedriver_win32\\chromedriver.exe");
		driver = new ChromeDriver();
		jsExec = (JavascriptExecutor) driver;
		listInitialJob = new ArrayList<String>();
		listJobResult = new ArrayList<JobData001>();
		searchDate = new SimpleDateFormat("MM/dd/yy")
				.format(new Date(System.currentTimeMillis() - 24 * 60 * 60 * 1000));
	}

	public void tearDown() throws Exception {
		// driver.quit();
	}

	public void setUpJquery() throws InterruptedException {
		driver.get("file:///F:/test/change.html");

		// Check for jQuery on the page, add it if need be
		jsExec.executeScript("if (!window.jQuery) {"
				+ "var jquery = document.createElement('script'); jquery.type = 'text/javascript';"
				+ "jquery.src = 'https://ajax.googleapis.com/ajax/libs/jquery/2.0.2/jquery.min.js';"
				+ "document.getElementsByTagName('head')[0].appendChild(jquery);" + "}");
		Thread.sleep(5000);

	}

	public static void findJobs(String jobName, String jobStatus, String jobDate) {

		String sb = "var jobItem = $(\"#_bookmark_1 tr:has(td:nth-child(2):contains('" + jobName
				+ "')):has(td:nth-child(3):contains('" + jobStatus + "')):has(td:nth-child(4):contains('" + jobDate
				+ "'))\");" + "return jobItem;";

		// System.out.println(sb);

		List<WebElement> listJobs = (List<WebElement>) jsExec.executeScript(sb);
		if (listJobs.size() > 0) {

			String sb1 = "var tivoliJob = $(\"#_bookmark_1 tr:has(td:nth-child(2):contains('" + jobName
					+ "')):has(td:nth-child(3):contains('" + jobStatus + "'))\");return tivoliJob;";

			String[] jobInfo = listJobs.get(0).getText().split(" ");

			listJobs.clear();
			listJobs = (List<WebElement>) jsExec.executeScript(sb1);
			StringBuilder strDates = new StringBuilder();

			if (listJobs.size() > 0) {
				for (WebElement str : listJobs) {

					strDates.append(str.getText().split(" ")[3]);
					strDates.append(",");
				}
			}

			listJobResult.add(new JobData001(jobInfo[0], jobInfo[1], jobInfo[2], strDates.toString()));
		}

	}

	public static void main(String[] args) throws Exception {
		// TODO Auto-generated method stub
		demoSearch o = new demoSearch();
		o.setUp();
		o.setUpJquery();
		o.ReadJobExcel();
		o.processJob();

		for (JobData001 jData : listJobResult) {
			System.out.println(jData.jobId + " | " + jData.jobName + " | " + jData.jobStatus + " | " + jData.jobDates);
		}

		System.out.println("=================");

	}

	private void processJob() {
		// TODO Auto-generated method stub
		if (listInitialJob.size() > 0) {
			for (String jobName : listInitialJob) {
				findJobs(jobName, "Waiting", searchDate);
			}
		}
	}

	private void ReadJobExcel() {
		listInitialJob.add("IGIGAGAIAECA");
		listInitialJob.add("CEAAIIABBABH");
	}

}
