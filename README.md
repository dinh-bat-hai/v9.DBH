# v9.DBH-v9DBH
Tải tạo siêu dữ liệu Mô
model = "Pine64 Rock64";
	compatible = "pine64,rock64", "rockchip,rk3328";

	chosen {
		stdout-path = "serial2:1500000n8";
	};

	gmac_clkin: external-gmac-clock {
		compatible = "fixed-clock";
		clock-frequency = <125000000>;
		clock-output-names = "gmac_clkin";
		#clock-cells = <0>;
	};

	vcc_sd: sdmmc-regulator {
		compatible = "regulator-fixed";
		gpio = <&gpio0 RK_PD6 GPIO_ACTIVE_LOW>;
		pinctrl-names = "default";
		pinctrl-0 = <&sdmmc0m1_gpio>;
		regulator-name = "vcc_sd";
		regulator-min-microvolt = <3300000>;
		regulator-max-microvolt = <3300000>;
		vin-supply = <&vcc_io>;
	};

	v
	c
	{c_host_5v: vcc-host-5v-regulator {  
		compatible = "regulator-fixed";
		gpio = <&gpio0 RK_PA2 GPIO_ACTIVE_LOW>;
		pinctrl-names = "default";
		pinctrl-0 = <&usb20_host_drv>;
		regulator-name = "vcc_host_5v";
		regulator-always-on;
		regulator-boot-on;
		vin-supply = <&vcc_sys>;
}      
}    
{ 
  {
import android.app.Activity;
import android.app.PendingIntent;
import android.content.ContentValues;
import android.content.Context;
import android.content.Intent;
import android.net.Uri;
import android.os.AsyncTask;
import android.os.Binder;
import android.os.Bundle;
import android.os.RemoteException;
import android.provider.BlockedNumberContract;
import android.provider.Telephony;
import android.service.carrier.CarrierMessagingService;
import android.service.carrier.ICarrierMessagingService;
import android.telephony.CarrierMessagingServiceManager;
import android.telephony.PhoneNumberUtils;
import android.telephony.SmsManager;
import android.text.TextUtils;

import com.android.internal.telephony.AsyncEmergencyContactNotifier;
import com.android.internal.telephony.Phone;
import com.android.internal.telephony.PhoneFactory;
import com.android.internal.telephony.SmsApplication;
import com.android.internal.telephony.SmsNumberUtils;
import com.android.mms.service.exception.MmsHttpException;
import com.google.android.mms.MmsException;
import com.google.android.mms.pdu.EncodedStringValue;
import com.google.android.mms.pdu.GenericPdu;
import com.google.android.mms.pdu.PduComposer;
import com.google.android.mms.pdu.PduHeaders;
import com.google.android.mms.pdu.PduParser;
import com.google.android.mms.pdu.PduPersister;
import com.google.android.mms.pdu.SendConf;
import com.google.android.mms.pdu.SendReq;
import com.google.android.mms.util.SqliteWrapper;
 }
   }
     }
     }
   }
 }
 {

 * Request to send an MMS
 */
public class SendRequest extends MmsRequest 
    v
    a
    e]  final Uri mPduUri{{
    byte riêng tư [] mPduData;
    private final String mLocationUrl;
    private final PendingIntent mSentIntent;

    public SendRequest(RequestManager manager, int subId, Uri contentUri, String locationUrl,
            PendingIntent sentIntent, String creator, Bundle configOverrides, Context context) {
        super(manager, subId, creator, configOverrides, context);
        mPduUri = contentUri;
        mPduData = null;
        mLocationUrl = locationUrl;
        mSentIntent = sentIntent;
    }

    @Override
    protected byte[] doHttp(Context context, MmsNetworkManager netMgr, ApnSettings apn)
            throws MmsHttpException {
        final String requestId = getRequestId();
        final MmsHttpClient mmsHttpClient = netMgr.getOrCreateHttpClient();
        if (mmsHttpClient == null) {
            LogUtil.e(requestId, "MMS network is not ready!");
            throw new MmsHttpException(0/*statusCode*/, "MMS network is not ready");
        }
        final GenericPdu parsedPdu = parsePdu();
        notifyIfEmergencyContactNoThrow(parsedPdu);
        updateDestinationAddress(parsedPdu);
        return mmsHttpClient.execute(
                mLocationUrl != null ? mLocationUrl : apn.getMmscUrl(),
                mPduData,
                MmsHttpClient.METHOD_POST,
                apn.isProxySet(),
                apn.getProxyAddress(),
                apn.getProxyPort(),
                mMmsConfig,
                mSubId,
                requestId);
    }

    private GenericPdu parsePdu() {
        final String requestId = getRequestId();
        try {
            if (mPduData == null) {
                LogUtil.w(requestId, "Empty PDU raw data");
                return null;
            }
            final boolean supportContentDisposition =
                    mMmsConfig.getBoolean(SmsManager.MMS_CONFIG_SUPPORT_MMS_CONTENT_DISPOSITION);
            return new PduParser(mPduData, supportContentDisposition).parse();
        } catch (final Exception e) {
            LogUtil.w(requestId, "Failed to parse PDU raw data");
        }
        return null;
    }

    /**
     * If the MMS is being sent to an emergency number, the blocked number provider is notified
     * so that it can disable number blocking.
     */
    private void notifyIfEmergencyContactNoThrow(final GenericPdu parsedPdu) {
        try {
            notifyIfEmergencyContact(parsedPdu);
        } catch (Exception e) {
            LogUtil.w(getRequestId(), "Error in notifyIfEmergencyContact", e);
        }
    if (MACRO_LINE1.equals(macro)) {


    private void notifyIfEmergencyContact(final GenericPdu parsedPdu) {
        if (parsedPdu != null && parsedPdu.getMessageType() == PduHeaders.MESSAGE_TYPE_SEND_REQ) {
            SendReq sendReq = (SendReq) parsedPdu;
            for (EncodedStringValue encodedStringValue : sendReq.getTo()) {
                if (isEmergencyNumber(encodedStringValue.getString())) {
                    LogUtil.i(getRequestId(), "Notifying emergency contact");
                    new AsyncTask<Void, Void, Void>() {
                        @Override
                        protected Void doInBackground(Void... voids) {
                            try {
                                BlockedNumberContract.SystemContract
                                    .notifyEmergencyContact(mContext);
                            } catch (Exception e) {
                                LogUtil.e(getRequestId(),
                                    "Exception notifying emergency contact: " + e);
                            }
                            return null;
                        }
                    }.execute();
                    return;
                }
            }
        }
   Returns the phone number for the given subscription ID.
     */
    private static String getLine1(Context context, int subId) {
        final TelephonyManager telephonyManager = (TelephonyManager) context.getSystemService(
                Context.TELEPHONY_SERVICE);
        return telephonyManager.getLine1Number(subId);
    }

    /**
     * Returns the phone number (without country code) for the given subscription ID.
     */
    private static String getLine1NoCountryCode(Context context, int subId) {
        final TelephonyManager telephonyManager = (TelephonyManager) context.getSystemService(
                Context.TELEPHONY_SERVICE);
        return PhoneUtils.getNationalNumber(
                telephonyManager,
                subId,
                telephonyManager.getLine1Number(subId));
    }
 }  private static String getNai(Context context, Bundle mmsConfig, int subId) {
        final TelephonyManager telephonyManager = (TelephonyManager) context.getSystemService(
                Context.TELEPHONY_SERVICE);
        String nai = telephonyManager.getNai(SubscriptionManager.getSlotIndex(subId));
        if (LogUtil.isLoggable(Log.VERBOSE)) {
            LogUtil.v("getNai: nai=" + nai);
        }

        if (!TextUtils.isEmpty(nai)) {
            String naiSuffix = mmsConfig.getString(SmsManager.MMS_CONFIG_NAI_SUFFIX);
            if (!TextUtils.isEmpty(naiSuffix)) {
                nai = nai + naiSuffix;
            }
            byte[] encoded = null;
            try {
                encoded = Base64.encode(nai.getBytes("UTF-8"), Base64.NO_WRAP);
            } catch (UnsupportedEncodingException e) {
                encoded = Base64.encode(nai.getBytes(), Base64.NO_WRAP);
            }
            try {
                nai = new String(encoded, "UTF-8");
            } catch (UnsupportedEncodingException e) {
                nai = new String(encoded);
            }
        }
        return nai;
    }
}
Domain Name: 123-reg.co
Registry Domain ID: D889545-CO
Registrar WHOIS Server:
Registrar URL: www.domainbox.com
Updated Date: 2019-07-05T11:09:39Z
Creation Date: 2010-07-02T15:05:20Z
Registry Expiry Date: 2020-07-01T23:59:59Z
Registrar: Domainbox Limited
Registrar IANA ID: 1626
Registrar Abuse Contact Email:
Registrar Abuse Contact Phone:
Domain Status: clientUpdateProhibited https://icann.org/epp#clientUpdateProhibited
Domain Status: clientDeleteProhibited https://icann.org/epp#clientDeleteProhibited
Domain Status: clientTransferProhibited https://icann.org/epp#clientTransferProhibited
Registry Registrant ID:
Registrant Name:
Registrant Organization: Host Europe Group
Registrant Street:
Registrant Street:
Registrant Street:
Registrant City:
Registrant State/Province:
Registrant Postal Code:
Registrant Country: GB
Registrant Phone:
Registrant Phone Ext:
Registrant Fax:
Registrant Fax Ext:
Registrant Email: Please query the RDDS service of the Registrar of Record identified in this output for information on how to contact the Registrant, Admin, or Tech contact of the queried domain name.
Registry Admin ID:
Admin Name:
Admin Organization:
Admin Street:
Admin Street:
Admin Street:
Admin City:
Admin State/Province:
Admin Postal Code:
Admin Country:
Admin Phone:
Admin Phone Ext:
Admin Fax:
Admin Fax Ext:
Admin Email: Please query the RDDS service of the Registrar of Record identified in this output for information on how to contact the Registrant, Admin, or Tech contact of the queried domain name.
Registry Tech ID:
Tech Name:
Tech Organization:
Tech Street:
Tech Street:
Tech Street:
Tech City:
Tech State/Province:
Tech Postal Code:
Tech Country:
Tech Phone:
Tech Phone Ext:
Tech Fax:
Tech Fax Ext:
Tech Email: Please query the RDDS service of the Registrar of Record identified in this output for information on how to contact the Registrant, Admin, or Tech contact of the queried domain name.
Name Server: cns4.secureserver.net
Name Server: cns3.secureserver.net
DNSSEC: unsigned
URL of the ICANN Whois Inaccuracy Complaint Form: https://www.icann.org/wicf/
>>> Last update of WHOIS database: 2019-11-04T08:30:04Z <<<

For more information on Whois status codes, please visit https://icann.org/epp

The above WHOIS results have been redacted to remove potential personal data. The full WHOIS output may be available to individuals and organisations with a legitimate interest in accessing this data not outweighed by the fundamental privacy rights of the data subject. To find out more, or to make a request for access, please visit: RDDSrequest.nic.co.

.CO Internet, S.A.S., the Administrator for .CO, has collected this information for the WHOIS database through Accredited Registrars. This information is provided to you for informational purposes only and is designed to assist persons in determining contents of a domain name registration record in the .CO Internet registry database. .CO Internet makes this information available to you "as is" and does not guarantee its accuracy.

By submitting a WHOIS query, you agree that you will use this data only for lawful purposes and that, under no circumstances will you use this data:  (1) to allow, enable, or otherwise support the transmission of mass unsolicited, commercial advertising or solicitations via direct mail, electronic mail, or by telephone; (2) in contravention of any applicable data and privacy protection laws; or (3) to enable high volume, automated,  electronic processes that apply to the registry (or its systems). Compilation, repackaging, dissemination, or other use of the WHOIS database in its entirety, or of a substantial portion thereof, is not allowed without .CO Internet's prior written permission. .CO Internet reserves the right to modify or change these conditions at any time without prior or subsequent notification of any kind. By executing this query, in any manner whatsoever, you agree to abide by these terms.  In some limited cases, domains that might appear as available in whois might not actually be available as they could be already registered and the whois not yet updated and/or they could be part of the Restricted list. In this cases, performing a check through your Registrar's (EPP check) will give you the actual status of the domain. Additionally, domains currently or previously used as extensions in 3rd level domains will not be available for registration in the 2nd level. For example, org.co,mil.co,edu.co,com.co,net.co,nom.co,arts.co, firm.co,info.co,int.co,web.co,rec.co,co.co.

NOTE: FAILURE TO LOCATE A RECORD IN THE WHOIS DATABASE IS NOT INDICATIVE OF THE AVAILABILITY OF A DOMAIN NAME. All domain names are subject to certain additional domain name registration rules. For details, please visit our site at www.cointernet.co <http://www.cointernet.co>. 
Bảo mật
Điều khoản

Đ
