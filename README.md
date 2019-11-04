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

	vcc_host_5v: vcc-host-5v-regulator {
		compatible = "regulator-fixed";
		gpio = <&gpio0 RK_PA2 GPIO_ACTIVE_LOW>;
		pinctrl-names = "default";
		pinctrl-0 = <&usb20_host_drv>;
		regulator-name = "vcc_host_5v";
		regulator-always-on;
		regulator-boot-on;
		vin-supply = <&vcc_sys>;
package com.android.mms.service;

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

/**
 * Request to send an MMS
 */
public class SendRequest extends MmsRequest {
    private final Uri mPduUri;
    private byte[] mPduData;
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
