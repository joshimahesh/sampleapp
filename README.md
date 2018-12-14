package com.bstkdemo.bskDemo;

import java.io.File;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.concurrent.TimeUnit;

import org.openqa.selenium.OutputType;
import org.openqa.selenium.Platform;
import org.openqa.selenium.TakesScreenshot;
import org.openqa.selenium.WebDriver;
import org.openqa.selenium.remote.Augmenter;
import org.openqa.selenium.remote.DesiredCapabilities;
import org.openqa.selenium.remote.RemoteWebDriver;
import org.testng.Assert;
import org.testng.annotations.DataProvider;
import org.testng.annotations.Test;

public class testNGTEST {
	public static final String USERNAME = "";
	public static final String AUTOMATE_KEY = "";
	public static final String URL = "https://" + USERNAME + ":" + AUTOMATE_KEY + "@hub-cloud.browserstack.com/wd/hub";

	@Test
	public void testMethod() throws MalformedURLException {

		DesiredCapabilities caps = new DesiredCapabilities();
		caps.setPlatform(Platform.MAC);
		caps.setBrowserName("firefox");
		caps.setVersion("57");
		caps.setCapability("browserstack.debug", true);
		URL brwstkurl = new URL(URL);
		WebDriver driver = new RemoteWebDriver(brwstkurl, caps);

		// My Account

		driver.get("https://www.phptravels.net/login");
		driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
		String actualURL = driver.getCurrentUrl();
		String currentURL = "https://www.phptravels.net/login";
		Assert.assertEquals("URL are not matched", actualURL, currentURL);
		driver.quit();

	}

	/*@Test(dataProvider = "bstkTestData")
	public void testMethod(Platform platform, String browserName, String browserVersion) throws MalformedURLException {

		DesiredCapabilities caps = new DesiredCapabilities();
		caps.setPlatform(platform);
		caps.setBrowserName(browserName);
		caps.setVersion(browserVersion);
		URL brwstkurl = new URL(URL);
		WebDriver driver = new RemoteWebDriver(brwstkurl, caps);

		// My Account

		driver.get("https://www.phptravels.net/login");
		driver.manage().timeouts().implicitlyWait(10, TimeUnit.SECONDS);
		
		
		driver = (RemoteWebDriver) new Augmenter().augment(driver);
		File srcFile = (File) ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);
		FileUtils.copyFile(srcFile,new File("/location/to/screenshot.png"));
		
		String actualURL = driver.getCurrentUrl();
		String currentURL = "https://www.phptravels.net/login";
		Assert.assertEquals("URL are not matched", actualURL, currentURL);
		driver.quit();

	}

	@DataProvider(name = "bstkTestData", parallel = true)
	public Object[][] getData() {
		Object[][] testData = new Object[][] { { Platform.MAC, "chrome", "62.0" }, { Platform.WIN8, "chrome", "62.0" },
				{ Platform.WINDOWS, "firefox", "57" } };
		return testData;

	}
*/
}
